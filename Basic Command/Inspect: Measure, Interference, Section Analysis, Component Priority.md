Tiếp phần **Inspect**. Nhóm này là nhóm duy nhất **không thay đổi gì cả** — không tạo hình học, không xuất hiện trong Timeline, không ảnh hưởng file xuất ra. Nó chỉ trả lời câu hỏi: *"Cái tôi vừa làm có đúng không?"*

Với người in 3D, đây thực ra là nhóm tiết kiệm nhiều thời gian nhất. Mỗi lần in thử một vỏ hộp trên K1C mất 2–4 giờ; 5 phút Inspect trước khi export thường tránh được đúng một vòng lặp đó.

Trước khi vào chi tiết, một lưu ý về danh sách bạn đưa: **Component Priority không nằm trong Inspect**. Nó nằm trong panel **Select** ở góc phải toolbar. Tôi sẽ giải thích nó ở mục 4 vì nó vẫn liên quan mật thiết (nó chính là thứ giúp bạn *chọn đúng đối tượng* để đo và kiểm tra).

| Feature | Trả lời câu hỏi |
|---|---|
| Measure | "Cái này dài/dày/nặng bao nhiêu?" |
| Interference | "Có chỗ nào hai chi tiết đụng nhau không?" |
| Section Analysis | "Bên trong nó trông thế nào?" |
| Component Priority | (Select panel) "Cho tôi chọn component, đừng bắt face" |

---

## 1. Measure (`I`)

**Ý nghĩa:** Đo mọi thứ. Nhưng Measure trong Fusion mạnh hơn "thước kẻ" nhiều — nó trả về cả một bảng thuộc tính, và số nó cho phụ thuộc vào **bạn chọn cái gì**.

### Kết quả theo số lượng selection

**Chọn 1 đối tượng** → Fusion trả về thuộc tính của chính nó:

| Chọn | Nhận được |
|---|---|
| Edge thẳng | Length |
| Edge tròn | Diameter, Radius, Length (chu vi), Center |
| Face phẳng | Area, Perimeter, Position |
| Face trụ | Diameter, Area, Axis |
| Body / Component | **Volume, Area, Mass, Density, Center of Mass, Bounding Box** |

**Chọn 2 đối tượng** → quan hệ giữa chúng:

| Kết quả | Ý nghĩa |
|---|---|
| **Distance** | Khoảng cách giữa hai điểm bắt (snap point) |
| **Minimum Distance** | Khoảng cách **ngắn nhất** giữa hai hình học |
| **Angle** | Góc giữa hai face/edge |
| **Delta X / Y / Z** | Chênh lệch theo từng trục |

⚠️ **Distance vs Minimum Distance** — đây là chỗ hay đọc sai số. Chọn hai mặt phẳng song song thì hai giá trị bằng nhau. Nhưng chọn mặt cắt chéo và mặt đáy thì `Distance` là khoảng cách giữa hai *điểm bắt* mà Fusion đoán, còn `Minimum Distance` là khe hở thực nhỏ nhất. **Khi kiểm tra clearance hay độ dày thành, luôn đọc Minimum Distance.**

### Các tham số trong dialog

- **Selection Filter** — dãy icon trên cùng: Face / Edge / Vertex / Body / Component / Sketch. Bật filter để Fusion không bắt sai đối tượng ở chỗ đông hình học. Với vỏ hộp có nhiều fillet, bật filter `Face` là cách duy nhất đo được ổn định.
- **Precision** — dropdown số chữ số thập phân, tới 6. **Nên tăng lên 3–4.** Mặc định làm tròn có thể hiển thị `40 mm` cho một giá trị thực là `39.9997` — nghĩa là mô hình bạn có sai số tích luỹ ở đâu đó, và bạn sẽ không thấy nếu để precision thấp.
- **Restart** — reset selection mà không đóng dialog.
- Click vào con số → **copy** vào clipboard. Dán trực tiếp vào ô dimension của feature khác được.
- Measure để mở suốt trong lúc làm việc cũng được, nó không khoá gì.

### Snap point — chỗ dễ sai nhất

Khi hover, Fusion tự bắt điểm. Nhấn giữ và di chuột trên cạnh để đổi giữa: **Endpoint / Midpoint / Center**. Nếu bạn đo hai lần cùng một thứ mà ra hai số khác nhau, gần như chắc chắn là snap point nhảy chỗ.

### ⚠️ Mass sai nếu chưa gán vật liệu

Fusion mặc định gán **Steel** cho mọi body mới. Nghĩa là Measure sẽ báo cái vỏ hộp của bạn nặng 340 g trong khi thực tế in PLA chỉ 45 g.

Sửa: `Modify → Physical Material` → Plastic → PLA (1.24 g/cm³) hoặc PETG / ABS. Sau đó Measure mới cho số dùng được.

Và ngay cả khi đã gán đúng: **Fusion tính như khối đặc 100%.** Vỏ in với 4 walls + 15% infill sẽ nhẹ hơn số Fusion báo khoảng 40–60%. Dùng số của Fusion làm **giới hạn trên** cho lượng filament, số thật lấy từ Creality Print.

### Bounding Box — đọc mục này khi in

Chọn body/component → phần **Bounding Box** cho `Length × Width × Height` của khối hộp nhỏ nhất bao quanh nó, **theo hệ trục hiện tại**.

Đây là cách duy nhất kiểm tra chi tiết có vừa khổ in trước khi export. K1C của bạn là 220×220×250 mm; nên lấy mốc an toàn **215×215×245** vì slicer còn cần chỗ cho brim/skirt và vùng exclude ở rìa bed.

Lưu ý: bounding box phụ thuộc hướng đặt. Chi tiết dài 240 mm không vừa theo X/Y nhưng có thể vừa nếu dựng theo Z — Measure không tự tìm hướng tối ưu giúp bạn.

### Ứng dụng cho hộp cắt chéo của bạn

Đây chính là thứ giải quyết câu hỏi kích thước khoang trong mà không cần tính tay:

```
Đo chiều cao khoang tại đầu cao:
  Chọn face đáy trong + face trần trong (mặt cắt chéo bên trong)
  → đọc Minimum Distance  ← đây là chỗ HẸP nhất, tức chỗ giới hạn

Đo độ dày thành ở mặt cắt chéo:
  Chọn face ngoài mặt cắt + face trong tương ứng
  → Minimum Distance phải = wall (1.6 mm)
  Nếu nhỏ hơn → Shell đã bị "bóp" ở chỗ góc nhọn

Kiểm tra board có vừa:
  Chọn Point Through Three Planes (góc lý thuyết khoang, mục Construct)
  + góc đối diện → Delta X/Y/Z
  → được kích thước khoang thật, kể cả khi fillet đã ăn mất vertex
```

---

## 2. Interference

**Ý nghĩa:** Tìm chỗ hai (hoặc nhiều) body/component **chiếm cùng một vùng không gian**. Đây là feature phát hiện lỗi assembly cổ điển.

**Cách dùng:**
1. Inspect → Interference.
2. Chọn **từ 2 đối tượng trở lên** (body hoặc component; chọn cả assembly cũng được).
3. Bấm **Compute**.
4. Đọc bảng kết quả.

**Kết quả** là một bảng liệt kê từng **cặp** giao nhau, kèm **Volume** của phần chồng lấn. Volume rất hữu ích để phân loại mức nghiêm trọng: 0.002 mm³ là sai số làm tròn, 340 mm³ là board thật sự đâm vào thành hộp.

**Hai option quan trọng:**

- **Include Coincident Faces** ⚠️ — mặc định **tắt**.
  - Tắt: chỉ báo chỗ chồng lấn có **thể tích thật**. Hai mặt tiếp xúc khít (volume = 0) được bỏ qua. Đây là cái bạn muốn 95% trường hợp.
  - Bật: báo cả trường hợp hai mặt *chạm* nhau. Dùng khi bạn muốn *xác nhận* hai chi tiết có tiếp xúc (vd. nắp có thật sự ngồi lên gờ không, hay đang hở 0.05 mm). Nhưng với assembly nhiều vít thì nó sinh ra hàng chục dòng nhiễu.

- **Create Interference Bodies** — checkbox trong khu vực kết quả. Bật → Fusion tạo body mới chính là **phần chồng lấn**. Cực kỳ hữu ích: bạn *thấy* được chỗ đụng nằm ở đâu và hình dạng thế nào, và có thể dùng luôn body đó làm tool cho `Combine → Cut` để khoét đúng chỗ cần khoét.

### ⚠️ Điểm quan trọng nhất: Interference = 0 KHÔNG có nghĩa là in ra lắp được

Đây là cái bẫy lớn nhất của feature này. Interference chỉ nói "không chồng lấn về mặt toán học". Nhưng:

- Interference = 0 với hai mặt sát nhau nghĩa là **khe hở đúng 0.000 mm** → in ra trên FDM thì hai chi tiết **không lắp vào nhau được**, hoặc phải mài.
- Bạn cần khe hở **dương**, và Interference không đo được nó.

**Cách làm đúng — dùng cả hai công cụ:**

| Mục đích | Công cụ |
|---|---|
| Phát hiện lỗi trắng trợn (board đâm vào thành) | **Interference** |
| Xác nhận khe hở đủ lớn (0.3 mm) | **Measure → Minimum Distance** |

**Thủ pháp rất tiện — "tool body phình":**

Bạn đã có sẵn các tool body đúng kích thước vật thật (board ESP32-C3, 18650, LD2410S). Làm thêm bước này:

```
1. Copy tool body ra một bản (Move/Copy → Create Copy)
2. Offset Face toàn bộ mặt của bản copy lên +0.3 mm
   → giờ nó là "vật thật + clearance yêu cầu"
3. Interference giữa bản phình này và vỏ hộp
4. Kết quả = 0  →  clearance thực tế ≥ 0.3 mm ở mọi chỗ  ✓
   Kết quả ≠ 0  →  chỗ nào chưa đủ, Create Interference Bodies để xem ngay
```

Cách này biến một câu hỏi định lượng khó ("khe hở nhỏ nhất là bao nhiêu, ở đâu?") thành một câu hỏi nhị phân dễ đọc.

### Interference không quét theo chuyển động

Interference chỉ kiểm tra **vị trí hiện tại** của assembly. Nắp bản lề của bạn đóng thì không đụng, nhưng ở góc 60° có thể đâm vào thành sau — Interference sẽ không biết.

| Cần | Dùng |
|---|---|
| Kiểm tra tại một vị trí | Interference |
| Kiểm tra suốt hành trình joint | Kéo joint tới vài vị trí rồi chạy lại Interference |
| Kiểm tra tự động, liên tục | `Assemble → Enable Contact Sets` + **Motion Study** |

---

## 3. Section Analysis

**Ý nghĩa:** Cắt mô hình để nhìn vào bên trong — nhưng **không phá gì cả**. Đây là điểm khác biệt cốt lõi so với `Split Body`.

**Cách dùng:**
1. Inspect → Section Analysis.
2. Chọn một **plane**: origin plane, construction plane, hoặc một **face phẳng** của body.
3. Xuất hiện gizmo → kéo mũi arrow để dịch mặt cắt, kéo cung để xoay.
4. Nhập `Distance` / `Angle` nếu cần chính xác.
5. OK.

**Các tham số:**
- **Distance / Angle X / Angle Y** — định vị mặt cắt bằng số.
- **Flip** — đảo bên nào bị cắt bỏ.
- Chọn được **nhiều đối tượng** để chỉ cắt một số body, giữ nguyên các body khác.

### Nó là một analysis "sống", không phải một feature

Đây là điểm cần hiểu rõ:

| | Section Analysis | Split Body |
|---|---|---|
| Trong Timeline | **Không** | Có |
| Thay đổi hình học | **Không** | Có |
| Ảnh hưởng file xuất / STL | **Không** | Có |
| Nằm ở đâu | Folder **Analysis** trong Browser | Folder Bodies |
| Bật/tắt | Icon con mắt, tức thời | Phải suppress/delete feature |
| Sửa lại | Double-click, kéo gizmo | Edit feature, có thể fail |

Vì nó nằm trong folder `Analysis` với icon con mắt, bạn có thể **giữ nhiều section analysis sẵn** trong file và bật cái cần dùng:

```
Analysis/
├── Section_Longitudinal   (qua trục dọc, xem toàn khoang)
├── Section_AtBatteryBay   (qua khoang pin 18650)
└── Section_AtSensor       (qua lỗ LD2410S trên nắp)
```

Bật **hai section analysis vuông góc** cùng lúc → được góc cutaway ¼, nhìn cả hai hướng một lúc. Rất tốt để chụp hình minh hoạ hoặc để giải thích thiết kế cho người khác.

### Phân biệt với các cách "nhìn vào trong" khác

| Cách | Đặc điểm |
|---|---|
| **Section Analysis** | Sống, không phá, giữ được, bật/tắt tự do. Dùng cái này. |
| **Slice** (checkbox trong Sketch Palette) | Chỉ ẩn phần trước mặt sketch, **chỉ khi đang trong sketch mode**. Tiện khi vẽ sketch bên trong khoang. |
| **Split Body** rồi ẩn một nửa | Phá hình học, thêm feature vào Timeline. Đừng dùng chỉ để xem. |
| **Section View** trong Drawing workspace | Feature riêng, để làm bản vẽ 2D có ghi kích thước. |

### Ứng dụng thực tế

- **Kiểm tra độ dày thành bằng mắt.** Shell tính đúng về mặt toán học, nhưng ở góc nhọn (như đầu nhọn của hộp cắt chéo của bạn) nó có thể cho thành mỏng hơn mong đợi hoặc tạo hình lạ. Section Analysis cho thấy ngay.
- **Xem ren.** Thread với `Modeled` bật — section analysis là cách duy nhất nhìn được profile ren để biết nó có ăn hết chiều sâu không.
- **Kiểm tra khoang.** Board ESP32-C3 nằm trong khoang có đủ chỗ cho connector USB-C ở cạnh không, gờ đỡ có che mất lỗ vít không.
- **Nhìn vào cả assembly.** Cắt qua toàn bộ vỏ + board + pin cùng lúc → thấy toàn bộ layout bên trong.
- **Kết hợp với Measure:** section cho bạn *thấy* chỗ đáng ngờ, rồi Measure vẫn snap vào edge/face thật để lấy số. Hai cái dùng chung là quy trình chuẩn để soát một vỏ hộp.

---

## 4. Component Priority (nằm ở panel **Select**)

Như đã nói ở đầu, feature này không thuộc Inspect. Nó nằm ở **panel `Select`** — góc phải cùng của toolbar, dropdown dưới icon con trỏ chuột.

**Ý nghĩa:** Một **selection filter**. Nó đặt ưu tiên chọn cho component: bấm một lần để chỉ chọn được component trong mô hình, bấm lại để bỏ ưu tiên và cho phép chọn mọi loại đối tượng.

**Cả họ Selection Priority:** các công cụ Selection Priority cho phép giới hạn selection chỉ còn solid/surface body, face trên body, edge trên body, hoặc component.

| Filter | Hiệu quả |
|---|---|
| **Select Component Priority** | Click đâu cũng ra **component** |
| Select Body Priority | Chỉ chọn được body |
| Select Face Priority | Chỉ chọn được face |
| Select Edge Priority | Chỉ chọn được edge |
| (bỏ hết) | Chọn được mọi thứ — mặc định |

**Vì sao cần nó:** trong assembly nhiều chi tiết, bạn muốn chọn *cái nắp* nhưng click vào thì Fusion cho bạn *một mặt của cái nắp*. Phải click vào Browser, hoặc click rồi Ctrl+click lên cấp — chậm và dễ sai. Bật Component Priority → click bừa lên canvas cũng ra component.

**Khi nào thực sự hữu ích:**
- Chọn nhanh 8 component cho **Rigid Group** (đúng mục bạn đã học ở phần Assemble).
- Chọn nhiều component để ẩn/hiện, hoặc gán appearance cho cả component.
- **Window select** cả một vùng để lấy hết component trong đó — không có filter thì window select sẽ bắt hàng trăm face.
- Chọn component cho `Move/Copy` hoặc `Joint` mà không bị bắt sai face.

⚠️ **Nhớ tắt khi xong.** Đang bật Component Priority thì bạn không chọn được face — và Fusion không nhắc bạn. Rất nhiều người tưởng phần mềm bị lỗi khi Fillet không chịu nhận edge, hoá ra là còn bật filter từ 20 phút trước.

**Hai option cùng dropdown, nên biết:**

- **Component Drag** — cho phép kéo component bằng chuột trực tiếp trên canvas. Nếu bạn hay vô tình xê dịch component khi định quay view, **tắt** nó. (Trong parametric mode, việc kéo component tạo ra một vị trí không được ghi vào Timeline — dễ gây lệch assembly mà không rõ nguyên nhân.)
- **Select Through** — chọn xuyên qua vật thể phía trước. Hữu ích khi cần chọn cái gì bên trong khoang mà không muốn ẩn vỏ.
- **Selection modes:** `Window` (mặc định — bao trọn mới chọn), `Freeform` (vẽ vùng tuỳ ý), `Paint` (quét như cọ). Freeform rất tiện để chọn một chuỗi edge cong bất quy tắc cho Fillet.

---

## Các Inspect còn lại (không trong danh sách bạn hỏi, nhưng có cái rất đáng dùng)

| Feature | Làm gì | Có đáng dùng với bạn |
|---|---|---|
| **Center of Mass** | Hiện tâm khối dưới dạng một điểm 3D | **Có.** Đèn cầu thang gắn tường: tâm khối lệch ra xa mặt tường tạo moment lớn hơn. Vật để đứng: tâm khối phải nằm trong đế, không thì đổ. Pin 18650 nặng ~45 g, chi phối tâm khối của cả cụm |
| **Draft Analysis** | Tô màu các face theo góc so với một hướng "pull" | **Có, rất đáng.** Đặt pull direction = **+Z** (hướng in) → face đỏ chính là **overhang > 45°** cần support. Đây là cách tìm chỗ cần đổi Fillet thành Chamfer, ngay trong CAD, trước khi vào slicer |
| **Minimum Radius Analysis** | Tìm bán kính cong nhỏ nhất trên mặt | Vừa phải. Chi tiết có bán kính nhỏ hơn ~0.2 mm sẽ không in ra được với nozzle 0.4 |
| **Curvature Comb Analysis** | "Lược" độ cong dọc một đường | Ít. Dùng cho spline cần liên tục G2 (thân xe, vỏ sản phẩm cong) |
| **Curvature Map Analysis** | Bản đồ độ cong trên bề mặt | Ít |
| **Zebra Analysis** | Vạch sọc phản chiếu, kiểm tra tiếp nối giữa các mặt | Ít. Dành cho class-A surface |
| **Accessibility Analysis** | Kiểm tra dao/đầu dò có tiếp cận được vùng nào | Không (dành cho CAM/đo kiểm) |
| **Component Color Cycling Toggle** | Tô mỗi component một màu tự động | **Có.** Cách nhanh nhất xác nhận cấu trúc component của bạn đúng — nếu có body nào không đổi màu, nó đang nằm ngoài mọi component |

Cái **Draft Analysis** đáng nhấn mạnh: nó được thiết kế cho khuôn nhựa, nhưng dùng cho FDM thì trùng khớp về nguyên lý. Hướng "pull" của khuôn ≈ hướng chồng lớp của máy in. Set pull = +Z rồi kéo slider tới 45° — bạn có ngay bản đồ overhang của toàn bộ chi tiết, chính xác hơn nhiều so với xoay mô hình rồi đoán bằng mắt.

---

## Checklist Inspect trước khi export — vỏ đèn cầu thang ESP32-C3

Chạy đúng thứ tự này mỗi lần trước khi `Save as Mesh`:

```
0. Modify → Physical Material → PLA hoặc PETG cho MỌI body
   (nếu bỏ bước này, mọi số về mass đều vô nghĩa)

1. Component Color Cycling Toggle  →  bật
   ✓ Mọi body đều đổi màu = không có body lơ lửng ngoài component

2. Measure → chọn từng component → đọc Bounding Box
   ✓ Mỗi chiều ≤ 215 / 215 / 245 mm

3. Interference toàn assembly, Include Coincident Faces = OFF
   ✓ Bảng trống. Nếu có, bật Create Interference Bodies để xem chỗ đụng

4. Tool body phình +0.3 mm  →  Interference lại
   ✓ Vẫn trống  =  clearance thực ≥ 0.3 mm cho board / pin / sensor

5. Section Analysis dọc trục  →  soi bằng mắt
   ✓ Thành không có chỗ mỏng bất thường ở đầu nhọn của mặt cắt chéo
   ✓ Không có gờ nào che lỗ vít
   ✓ Có chỗ cho connector USB-C thò ra

6. Measure → Minimum Distance ở chỗ thành mỏng nhất thấy được
   ✓ ≥ 1.2 mm (3 walls). Mục tiêu 1.6 mm (4 walls)

7. Draft Analysis, pull = +Z, ngưỡng 45°
   ✓ Không có face đỏ ở chỗ không muốn support
   → Chỗ nào đỏ ở cạnh đáy: đổi Fillet thành Chamfer 0.5 mm

8. Center of Mass
   ✓ Nếu gắn tường: tâm khối càng gần tường càng tốt
   ✓ Nếu để đứng: hình chiếu tâm khối nằm trong đế

9. Measure → Volume × 1.24 g/cm³
   → Giới hạn trên của filament. Số thật ~40–60% con này
```

Bước 4 và bước 7 là hai bước hay bị bỏ, và cũng là hai nguyên nhân in lại phổ biến nhất.

---

## Tổng kết

| Bạn muốn | Dùng |
|---|---|
| Kiểm tra khe hở thực giữa hai chi tiết | **Measure → Minimum Distance** (không phải Distance) |
| Chi tiết có vừa khổ in K1C không | **Measure → Bounding Box** |
| Khối lượng / lượng filament ước tính | Gán **Physical Material** trước, rồi Measure → Volume, Mass |
| Đo tới góc khoang đã bị fillet ăn mất | Construction Point + Measure → Delta X/Y/Z |
| Phát hiện board đâm vào thành hộp | **Interference** |
| Xác nhận clearance đủ 0.3 mm ở mọi chỗ | Tool body phình +0.3 → **Interference** = 0 |
| Xem chỗ đụng nằm chính xác ở đâu | Interference → **Create Interference Bodies** |
| Xác nhận hai mặt có tiếp xúc (không hở) | Interference → **Include Coincident Faces** ON |
| Nhìn vào bên trong khoang | **Section Analysis** (không phải Split Body) |
| Kiểm tra profile ren đã Modeled | **Section Analysis** |
| Giữ sẵn nhiều góc nhìn cắt để bật/tắt | Nhiều **Section Analysis** trong folder Analysis |
| Vẽ sketch bên trong khoang mà bị vỏ che | **Slice** trong Sketch Palette |
| Chọn nhanh 8 component cho Rigid Group | **Select Component Priority** (Select panel) |
| Hết bị vô tình xê dịch component | Tắt **Component Drag** |
| Chọn chuỗi edge cong bất quy tắc cho Fillet | Selection mode **Freeform** |
| Tìm mọi overhang > 45° trước khi in | **Draft Analysis**, pull = +Z |
| Kiểm tra cấu trúc component đúng chưa | **Component Color Cycling Toggle** |
| Chi tiết có bị đổ / moment gắn tường | **Center of Mass** |
| Kiểm tra đụng độ suốt hành trình chuyển động | **Contact Sets + Motion Study** (không phải Interference) |

---

Vậy là xong bốn nhóm chính của Solid tab: **Create → Modify → Assemble → Construct → Inspect**. Bạn đã có toàn bộ bộ công cụ để dựng và soát một chi tiết cơ khí.

Thứ còn thiếu, và cũng là thứ quyết định mô hình của bạn *bền* hay *vỡ khi sửa*, là **Sketch**: constraint (Horizontal, Coincident, Tangent, Equal, Symmetry…), dimension, và khái niệm **fully constrained** — sketch chuyển màu khi mọi bậc tự do đã bị khoá. Đây là nền móng thật của parametric modeling; ba nhóm trên chỉ mạnh khi sketch bên dưới chặt chẽ.

Song song đó còn nhóm **Create nâng cao** (Sweep, Loft, Rib, Web, Emboss, Thicken) và nhóm **Pattern / Mirror** — hai cái này bạn sẽ cần cho boss vít và tản nhiệt.

Bạn muốn đi tiếp nhóm nào?
