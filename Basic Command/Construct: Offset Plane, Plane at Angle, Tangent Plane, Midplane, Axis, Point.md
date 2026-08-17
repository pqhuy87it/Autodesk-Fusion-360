Tiếp phần **Construct**. Nhóm này khác biệt cơ bản so với ba nhóm trước: nó **không tạo vật liệu**. Mọi thứ ở đây là *reference geometry* — hình học tham chiếu, dùng để "bám" vào chứ không phải để in ra.

Vì thế nó có hai đặc tính quan trọng:

- **Miễn phí hoàn toàn.** Không có khối lượng, không xuất ra STL, slicer không thấy. Cứ tạo bao nhiêu cũng được.
- **Nó là thứ giúp mô hình *parametric*.** Đây là điểm cốt lõi: mỗi khi bạn thấy mình phải canh tay, kéo chuột đoán vị trí, hoặc dùng `Move` để đặt chi tiết — nghĩa là bạn đang thiếu một construct geometry.

Bức tranh tổng thể:

| Loại | Cái bạn có được | Dùng để làm gì |
|---|---|---|
| **Plane** | Một mặt phẳng | Vẽ sketch, làm dao cắt (Split Body), làm mặt Mirror |
| **Axis** | Một trục vô hạn | Trục Revolve, trục Circular Pattern, pivot cho Plane at Angle |
| **Point** | Một điểm | Định vị Hole, Joint Origin, đỉnh của Loft, mốc đo |

---

## 1. Offset Plane

**Ý nghĩa:** Mặt phẳng song song, cách một mặt phẳng gốc một khoảng cho trước. Đơn giản nhất và dùng nhiều nhất trong cả nhóm.

**Cách dùng:**
1. Construct → Offset Plane.
2. Chọn một **origin plane**, **construction plane** khác, hoặc một **face phẳng** của body.
3. Nhập Distance (số âm để đảo chiều), hoặc kéo mũi arrow.

**Tham số:** chỉ có `Distance`. Nhưng đây là chỗ nên dùng **parameter** thay vì số cứng:

Vào `Modify → Change Parameters`, tạo user parameter `pcb_height = 4 mm`, rồi ô Distance nhập `pcb_height`. Sau này đổi chiều cao boss đỡ board chỉ cần sửa một chỗ.

**Vấn đề then chốt — chọn gì làm mặt gốc:**

| Offset từ | Độ bền |
|---|---|
| **Origin plane** (XY/XZ/YZ) | Bền nhất. Origin không bao giờ biến mất |
| Construction plane khác | Bền, nhưng tạo chuỗi phụ thuộc — sửa cái gốc thì cả chuỗi dịch theo |
| **Face của body** | Yếu nhất. Nếu feature tạo ra face đó bị sửa, face có thể đổi ID → plane báo lỗi |

Cái thứ ba là nguyên nhân của phần lớn dấu chấm than vàng trong Timeline. Nó có tên riêng trong CAD: **topological naming problem**. Nguyên tắc: nếu bạn *có thể* offset từ Origin, hãy offset từ Origin.

**Nhưng cũng có lúc offset từ face là đúng:** khi *ý định thiết kế* thực sự là "cách mặt trên 3 mm" — ví dụ mặt phẳng vẽ logo luôn phải nằm dưới mặt nắp 0.4 mm bất kể nắp cao bao nhiêu. Lúc đó liên kết với face lại chính là điều bạn muốn.

**Ứng dụng thực tế:**
- Vẽ sketch ở một độ cao mà không cần extrude khối trung gian.
- Tạo các section cho `Loft`.
- Làm dao cắt cho `Split Body` — tách thân/nắp ở đúng độ cao.
- Đặt mặt phẳng cho `Mirror`.

---

## 2. Plane at Angle

**Ý nghĩa:** Mặt phẳng đi qua một **đường thẳng** và nghiêng một góc. Đây chính là công cụ bạn cần cho **cái hộp có mặt cắt chéo** mà bạn đang làm.

**Cách dùng:**
1. Construct → Plane at Angle.
2. Chọn **một đường thẳng** làm trục bản lề — có thể là: linear edge của body, sketch line, construction axis, hoặc trục Origin X/Y/Z.
3. Nhập Angle.

**Yêu cầu:** bắt buộc phải có một đường thẳng. Không chọn được face, không chọn được đường cong.

**Vì sao đây là cách đúng cho hộp cắt chéo:**

So sánh ba cách tạo mặt cắt chéo:

| Cách | Vấn đề |
|---|---|
| Vẽ sketch line chéo rồi Extrude Cut | Được, nhưng góc bị "chôn" trong dimension của sketch, khó nhìn ra |
| Split Body bằng một face của body phụ | Phụ thuộc body phụ, nặng và dễ lỗi |
| **Plane at Angle** + Split Body | Góc là **một tham số hiển thị rõ ràng**, sửa một chỗ là xong |

Quy trình khuyến nghị:

```
1. Tạo user parameter: cut_angle = 30 deg
2. Extrude hộp thành khối đặc (chưa cắt)
3. Construct → Plane at Angle
   - Line: cạnh trên của hộp (hoặc trục Origin nếu đặt hộp đối xứng)
   - Angle: cut_angle
4. Rename plane thành "Plane_DiagonalCut"
5. Split Body, Splitting Tool = plane đó
6. Xoá nửa không cần
```

Sau đó muốn thử 25° hay 35° chỉ cần sửa `cut_angle` → toàn bộ mô hình tự cập nhật, kể cả khoang trong nếu bạn đã Shell/Combine ở phía sau.

**Lưu ý về mốc 0°:** Fusion tự chọn một mặt phẳng tham chiếu để tính góc, và nó không luôn là cái bạn nghĩ. Nếu plane nghiêng sai hướng, nhập **góc âm** hoặc `180° − góc`. Đừng cố sửa bằng cách chọn lại edge khác.

**Ứng dụng khác:** vẽ sketch trên mặt vát, tạo draft angle cho khuôn, dựng mặt cắt xiên để lấy mặt cắt minh hoạ, tạo bề mặt nghiêng hắt sáng cho đèn.

---

## 3. Tangent Plane

**Ý nghĩa:** Mặt phẳng tiếp tuyến với một **mặt cong** — cylindrical, conical, hoặc spherical. Bạn được một "mặt phẳng dán lên thân trụ".

**Cách dùng:**
1. Construct → Tangent Plane.
2. Chọn mặt trụ / mặt nón / mặt cầu.
3. (Tùy chọn) chọn thêm một **reference plane** để xác định vị trí điểm tiếp tuyến.
4. Nhập **Angle** — quay điểm tiếp tuyến quanh trục của mặt trụ.

**Vì sao cần nó:** Fusion **không cho vẽ sketch trên mặt cong**. Muốn đặt bất cứ thứ gì lên thân trụ, bạn cần một mặt phẳng tiếp tuyến để vẽ trên đó rồi extrude vào/ra.

**Ứng dụng thực tế:**
- **Tạo mặt phẳng ("flat") trên thân trụ** để bắt vít, dán nhãn, hoặc đặt module cảm biến. Vẽ rectangle trên tangent plane → Extrude Cut vào → có chỗ phẳng để LD2410S ngồi.
- **Emboss / khắc chữ, logo lên vật hình trụ.**
- Tạo lỗ khoan xuyên tâm ở một vị trí góc cụ thể trên thân ống.
- Làm mặt tham chiếu cho `Hole` trên thân trụ (Hole cần face phẳng).

**Kết hợp mạnh:** Tangent Plane + tham số Angle + `Circular Pattern` → tạo 6 mặt phẳng cách nhau 60° quanh thân trụ để bắt 6 vít.

**Lưu ý:** với mặt cầu, điểm tiếp tuyến khó kiểm soát hơn nhiều; thường nên dùng `Plane Through Three Points` hoặc offset từ Origin rồi rotate thì dễ đoán hơn.

---

## 4. Midplane

**Ý nghĩa:** Mặt phẳng ở **chính giữa** hai mặt phẳng / hai face phẳng.

**Cách dùng:**
1. Construct → Midplane.
2. Chọn **Face 1** và **Face 2** (planar face hoặc construction/origin plane).
3. Xong — không có tham số nào để nhập.

**Hai trường hợp:**
- Hai mặt **song song** → mặt phẳng song song ở giữa, cách đều.
- Hai mặt **không song song** → mặt phẳng **phân giác** (bisector) của góc giữa chúng. Ít dùng hơn nhưng đúng là hành vi của nó.

**Ưu điểm lớn nhất: nó tự cập nhật.** Đây là điểm hơn hẳn Offset Plane. Nếu hộp của bạn cao 40 mm, Midplane nằm ở 20 mm. Đổi chiều cao thành 50 mm → Midplane **tự** về 25 mm. Còn `Offset Plane 20 mm` thì vẫn nằm ở 20 mm và làm sai toàn bộ những gì phụ thuộc vào nó.

Nguyên tắc: **cần mặt phẳng ở giữa thì dùng Midplane, đừng bao giờ dùng Offset Plane với con số = một nửa.**

**Ứng dụng thực tế:**
- **Mặt phẳng đối xứng cho `Mirror`.** Đây là ứng dụng số một. Mô hình một nửa vỏ hộp → Mirror qua Midplane.
- Tìm mặt phẳng đối xứng của **body import từ STEP/STL** — bạn không có Origin đúng chỗ, nhưng có hai mặt bên; Midplane cho ngay mặt đối xứng.
- Làm mặt cắt tách thân/nắp đúng giữa mà vẫn tự cập nhật khi hộp đổi chiều cao.
- Mặt tham chiếu để `Joint` hai chi tiết đối xứng.

---

## 5. Axis (nhóm 6 feature)

Axis là một **đường thẳng vô hạn**, không có hình học. Vì sao cần nó khi đã có edge? Vì edge **hữu hạn và dễ mất ID**, còn nhiều feature (Revolve, Circular Pattern, Plane at Angle) cần trục kéo dài quá phạm vi body.

| Feature | Chọn gì | Kết quả |
|---|---|---|
| **Axis Through Cylinder/Cone/Torus** | Một mặt trụ / nón / torus | Trục đối xứng của nó |
| **Axis Perpendicular at Point** | Một face/plane + một point | Trục vuông góc mặt đó, đi qua point |
| **Axis Through Two Planes** | Hai mặt phẳng cắt nhau | Đường giao tuyến |
| **Axis Through Two Points** | Hai vertex / point | Đường qua hai điểm |
| **Axis Through Edge** | Một linear edge | Trục nằm trùng edge, nhưng vô hạn |
| **Axis Perpendicular to Face at Point** | Một face (kể cả cong) + point trên đó | Pháp tuyến tại điểm đó |

**Cái dùng nhiều nhất, cách biệt rất xa: Axis Through Cylinder/Cone/Torus.** Chỉ cần click vào thân một lỗ hoặc một trụ là có trục ngay. Đừng vẽ construction line để làm việc này.

**Ứng dụng thực tế:**
- **Circular Pattern:** cần một axis. Pattern 4 lỗ vít quanh tâm nắp tròn → `Axis Through Cylinder` của thân nắp.
- **Revolve axis:** thay vì vẽ construction line trong sketch, dùng axis có sẵn → mô hình bền hơn.
- **Pivot cho `Plane at Angle`:** khi không có edge thẳng nào ở đúng chỗ, tạo axis trước.
- **`Axis Through Two Planes`** để lấy giao tuyến của hai mặt vát — chính là cạnh lý thuyết cần fillet hoặc cần làm trục bản lề.
- Trục quay cho Revolute Joint khi không có cạnh tròn nào để snap.

**Tip:** `Axis Through Edge` nghe vô nghĩa (đã có edge rồi mà?) nhưng rất hữu ích: nó **cắt chuỗi phụ thuộc**. Bạn tạo axis từ edge một lần, rồi mọi feature sau đó tham chiếu **axis** thay vì edge. Nếu edge đổi ID, bạn chỉ cần sửa lại đúng một feature (cái axis) thay vì mười feature phía sau.

---

## 6. Point (nhóm 6 feature)

Construction point là một điểm tham chiếu trong không gian 3D. Nghe tầm thường nhưng nó mở ra vài thứ không làm được bằng cách khác.

| Feature | Chọn gì | Kết quả |
|---|---|---|
| **Point at Vertex** | Một vertex, sketch point, hoặc điểm đầu edge | Điểm tại đó |
| **Point Through Two Edges** | Hai edge cắt/gần cắt nhau | Giao điểm |
| **Point Through Three Planes** | Ba mặt phẳng | Giao điểm ba mặt |
| **Point at Center of Circle/Sphere/Torus** | Cạnh tròn / mặt cầu / torus | **Tâm** của nó |
| **Point at Edge and Plane** | Một edge + một plane | Nơi edge xuyên qua plane |
| **Point Along Path** | Một edge/curve + tỉ lệ 0–1 | Điểm tại % chiều dài đó |

**Ứng dụng thực tế:**

- **Định vị `Hole`.** `Hole → Placement: From Sketch` nhận cả construction point. Kết hợp `Point at Center of Circle` để khoan lỗ đồng tâm với một trụ có sẵn — chính xác tuyệt đối, không cần dimension.
- **Joint Origin.** Khi vị trí lắp không có snap point nào tự nhiên (lệch tâm, ở giữa không khí), tạo construction point rồi joint vào nó.
- **Đỉnh của `Loft`.** Loft cho phép profile cuối là một **điểm** → đây là cách tạo hình nón/chóp có mặt cắt tuỳ ý. Không có construction point thì không làm được.
- **`Point Along Path` với tham số** — đặt vật ở 70% chiều dài một đường cong, và nếu đường cong dài ra thì vật tự dịch theo. Hữu ích cho việc rải LED dọc một đường cong, hoặc đặt kẹp giữ dây.
- **Mốc đo.** `Point Through Three Planes` cho một góc lý thuyết của khoang trong — đúng thứ bạn cần khi tính kích thước cavity sau khi cắt chéo, vì góc thật đã bị fillet ăn mất và không còn vertex để click vào.
- Điểm đích cho `Move → Point to Point`.

---

## Các plane còn lại trong menu (không nằm trong danh sách bạn hỏi, nhưng nên biết)

| Feature | Cách hoạt động | Khi nào cần |
|---|---|---|
| **Plane Through Two Edges** | Hai edge đồng phẳng | Nhanh hơn Three Points khi đã có sẵn hai cạnh |
| **Plane Through Three Points** | Ba point / vertex bất kỳ | Cách **linh hoạt nhất**. Định nghĩa mặt cắt chéo bằng 3 góc cụ thể của hộp — trực quan hơn nhập góc |
| **Plane Along Path** | Một path + tỉ lệ 0–1 | Mặt phẳng vuông góc đường cong tại vị trí đó. Dùng cho Loft/Sweep theo đường cong, hoặc lấy mặt cắt ống |

Với hộp cắt chéo, **Plane at Angle** và **Plane Through Three Points** là hai lựa chọn tương đương về kết quả nhưng khác về ý định:

- Nếu bạn nghĩ "cắt nghiêng 30°" → `Plane at Angle`.
- Nếu bạn nghĩ "cắt từ điểm cao 40 mm bên này xuống điểm cao 15 mm bên kia" → `Plane Through Three Points`, dùng ba sketch point có dimension.

Cách thứ hai thường đúng hơn với hộp thật, vì bạn quan tâm **chiều cao hai đầu** chứ không quan tâm góc là bao nhiêu độ.

---

## Ba nguyên tắc quản lý construct geometry

**1. Đặt tên ngay.** `Plane1`, `Plane2`, `Axis1` sẽ vô nghĩa sau một tuần. Dùng `Plane_CutDiagonal`, `Plane_PCBTop`, `Mid_Symmetry`, `Axis_HingePin`. Chúng nằm trong folder **Construction** ở Browser (Axis và Point cũng nằm chung folder này).

**2. Tắt visibility khi không dùng.** Construct nhiều lên là viewport rối không nhìn được gì. Tắt cả folder `Construction` bằng một click vào icon con mắt. Chúng vẫn hoạt động bình thường khi bị ẩn.

**3. Chú ý component đang active.** Construct geometry được tạo **bên trong component đang active**. Nếu bạn tạo plane ở root rồi dùng nó bên trong một component, Fusion tạo **external reference** — component đó bị phụ thuộc ra ngoài, và khi bạn export riêng component sẽ gặp vấn đề. Nguyên tắc: plane dùng cho component nào thì activate component đó trước rồi hãy tạo.

---

## Áp dụng cho hộp cắt chéo của bạn

Bạn đang tính kích thước khoang trong sau khi cắt — construct geometry giải quyết đúng việc đó, và giải quyết theo cách không phải tính tay:

```
1. Parameters:  wall = 1.6 mm
                h_tall = 40 mm
                h_short = 15 mm

2. Extrude hộp đặc theo kích thước NGOÀI

3. Construct → Plane Through Three Points
   Ba point ở độ cao h_tall và h_short (có dimension trong sketch)
   → Rename: Plane_CutDiagonal

4. Split Body bằng plane đó → xoá phần trên

5. Shell (Direction: Inside, wall) → chọn bỏ mặt cắt chéo
   → Fusion tự tính khoang trong, KHÔNG cần bạn tính hình học nào

6. Construct → Offset Plane từ Plane_CutDiagonal, distance = -wall
   → Rename: Plane_InnerCeiling
   → Đây là mặt trần trong. Sketch trên nó để kiểm tra board có vừa không.

7. Construct → Point Through Three Planes
   (mặt trong đáy + hai mặt trong hông)
   → Góc lý thuyết của khoang. Dùng Measure từ point này để lấy số thật.

8. Midplane từ hai mặt hông ngoài
   → Mirror boss vít, đảm bảo đối xứng tuyệt đối
```

Điểm quan trọng: sau bước này, đổi `h_tall`, `h_short`, hay `wall` thì **mọi thứ tự cập nhật**, kể cả các con số bạn đo. Bạn không phải tính lại lần nào nữa. Cái Point ở bước 7 đặc biệt hữu ích vì góc thật của khoang đã bị fillet của Shell ăn mất, không có vertex nào để click.

Với đèn cầu thang: nếu vỏ có phần thân trụ, `Tangent Plane` là cách để tạo mặt phẳng bắt LD2410S vào thân cong mà vẫn giữ được sketch parametric.

---

## Tổng kết

| Bạn muốn | Dùng |
|---|---|
| Vẽ sketch ở một độ cao nhất định | **Offset Plane** (từ Origin plane) |
| Mặt phẳng ở giữa hai mặt, tự cập nhật khi đổi kích thước | **Midplane** (**không** phải Offset Plane với số /2) |
| Định nghĩa mặt cắt chéo bằng **góc** | **Plane at Angle** |
| Định nghĩa mặt cắt chéo bằng **chiều cao hai đầu** | **Plane Through Three Points** |
| Vẽ / bắt vít / khắc chữ trên thân trụ | **Tangent Plane** |
| Trục cho Revolve hoặc Circular Pattern | **Axis Through Cylinder/Cone/Torus** |
| Cạnh lý thuyết nơi hai mặt vát gặp nhau | **Axis Through Two Planes** |
| Cắt chuỗi phụ thuộc vào edge dễ mất ID | **Axis Through Edge**, rồi tham chiếu axis |
| Khoan lỗ đồng tâm với trụ có sẵn | **Point at Center of Circle** + Hole From Sketch |
| Đỉnh nhọn cho Loft | **Point at Vertex** / construction point |
| Đo tới góc khoang đã bị fillet ăn mất | **Point Through Three Planes** |
| Đặt vật ở 70% chiều dài đường cong | **Point Along Path** |
| Mặt phẳng vuông góc với đường cong | **Plane Along Path** |

Còn lại trong Solid tab là **Inspect** (Measure, Interference, Section Analysis, Curvature Comb) — nhóm này ít "tạo ra" gì nhưng `Interference` là thứ sẽ nói cho bạn biết board ESP32 có đụng vào thành hộp hay không trước khi in, và `Section Analysis` là cách nhìn vào bên trong khoang mà không cần Split Body. Đi tiếp nhóm đó, hay bạn muốn chuyển sang **Sketch** (constraint và dimension — nền tảng thực sự của parametric modeling)?
