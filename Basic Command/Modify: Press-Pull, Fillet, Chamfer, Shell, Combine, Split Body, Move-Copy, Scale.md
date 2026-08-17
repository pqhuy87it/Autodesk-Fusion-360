Tiếp phần **Modify** (Design → Solid → Modify). Nhóm này khác Create ở chỗ: nó **sửa hình học đã có**, nên thứ tự trong Timeline cực kỳ quan trọng — cùng một bộ feature nhưng đặt sai thứ tự sẽ ra kết quả khác nhau.

Chia nhóm nhanh:

| Nhóm | Feature | Bản chất |
|---|---|---|
| Xử lý cạnh | Fillet, Chamfer | Làm tròn / vát cạnh |
| Xử lý khối | Shell, Combine, Split Body | Rỗng hoá, boolean, chia tách |
| Biến đổi | Move/Copy, Scale | Đổi vị trí, đổi kích thước |
| Tổng hợp | Press/Pull | Bộ điều phối thông minh |

---

## 1. Press/Pull (`Q`)

**Ý nghĩa:** Không phải một feature riêng, mà là một **dispatcher** — Fusion xem bạn chọn gì rồi tự gọi feature phù hợp. Trong Timeline nó ghi lại đúng feature thật (Extrude / Offset Face / Fillet), chứ không phải "Press Pull".

**Ánh xạ selection → hành vi:**

| Bạn chọn | Fusion thực thi |
|---|---|
| Sketch profile kín | **Extrude** |
| Một face phẳng | **Offset Face** (đẩy face, giữ nguyên các face xung quanh) |
| Nhiều face / face cong | **Offset Face** |
| Một edge | **Fillet** |

**Vì sao nên dùng:** nhanh hơn nhiều so với vào menu. Quy trình thực tế: bấm `Q`, click face, kéo mũi arrow, nhập số, Enter.

**Điểm cần hiểu rõ — Offset Face ≠ Extrude:**
- `Extrude` trên một face sẽ *thêm/bớt vật liệu theo hướng pháp tuyến*, tạo bậc.
- `Offset Face` **dịch chuyển cả face đó** và tự động kéo dài/co lại các face lân cận. Đây là cách đúng để "làm dày thêm 1 mm cho toàn bộ thành hộp" hoặc "nới lỗ ra 0.2 mm".

**Tip cho project của bạn:** khi vỏ hộp ESP32 in ra bị chật, đừng sửa sketch. Bấm `Q`, chọn 4 mặt trong của khoang, offset `−0.15 mm` → nới đều toàn bộ khoang trong một bước.

---

## 2. Fillet (`F`)

**Ý nghĩa:** Làm tròn cạnh. Ngoài mục đích thẩm mỹ, fillet có tác dụng kỹ thuật thật: **giảm tập trung ứng suất** ở góc trong (stress concentration). Góc trong vuông là nơi nứt đầu tiên khi chi tiết chịu tải — với chi tiết in FDM thì điều này càng đúng vì lớp in vốn đã yếu theo trục Z.

**Ba loại chính (dropdown trên cùng dialog):**

- **Edge Fillet** — loại thường dùng. Chọn edge/face/feature.
- **Rule Fillet** — fillet theo *quy tắc*: chọn một feature, Fusion tự tìm mọi cạnh thoả điều kiện (vd. tất cả cạnh giao giữa feature này và body). Rất mạnh khi bạn sẽ còn sửa hình học — thêm cạnh mới thì fillet tự áp vào.
- **Full Round Fillet** — chọn 3 face (side – center – side), Fusion thay face giữa bằng một cung tròn bán kính lớn nhất có thể. Dùng làm mép tay cầm, đầu thanh bo tròn hoàn toàn. Không cần nhập bán kính.

**Các option trong Edge Fillet:**
- **Fillet Type:**
  - `Constant Radius` — bán kính đều.
  - `Variable Radius` — bán kính thay đổi dọc cạnh; click thêm point trên edge để chèn giá trị trung gian.
  - `Chord Length` — nhập chiều dài **dây cung** thay vì bán kính. Hữu ích khi bạn quan tâm "ăn vào bao nhiêu mm" hơn là bán kính.
- **Tangent Chain** — bật (default): chọn 1 cạnh thì tự lấy cả chuỗi cạnh tiếp tuyến liền mạch. Tắt nó khi bạn chỉ muốn fillet đúng một cạnh trong chuỗi.
- **Corner Type:** `Rolling Ball` (mô phỏng viên bi lăn — tự nhiên hơn) / `Setback` (fillet lùi lại ở góc 3 cạnh gặp nhau, cho ra góc mượt kiểu sản phẩm nhựa).
- Chọn **face** thay vì edge → fillet toàn bộ cạnh của face đó. Chọn **feature trong Timeline** → fillet mọi cạnh của feature.

**Khi Fillet fail:** gần như luôn vì bán kính quá lớn so với hình học lân cận (thành mỏng hơn 2×R, hoặc hai fillet chồng nhau). Cách xử lý: giảm R, hoặc tách thành nhiều Fillet feature riêng và làm cạnh lớn trước, cạnh nhỏ sau.

**Với in 3D (K1C):**
- Fillet ở **góc trong** (nơi thành gặp đáy) là miễn phí về mặt in ấn và tăng độ bền đáng kể → nên làm.
- Fillet ở **cạnh dưới cùng** tiếp giáp bed sẽ tạo overhang gần 90° ở mm đầu → in xấu, dễ có elephant foot. Chỗ này dùng **Chamfer** thay vì Fillet.

---

## 3. Chamfer

**Ý nghĩa:** Vát cạnh phẳng. Về mặt gia công/lắp ráp, chamfer quan trọng hơn fillet: nó là thứ giúp chi tiết **dẫn hướng vào nhau** khi lắp (lead-in), khử ba-via, và giúp đầu vít tự định tâm.

**Các tham số:**
- **Chamfer Type:**
  - `Equal Distance` — vát 45°, nhập một khoảng. 90% trường hợp dùng cái này.
  - `Two Distances` — hai khoảng khác nhau → góc vát khác 45°.
  - `Distance and Angle` — nhập khoảng + góc. Dùng khi cần đúng một góc cụ thể (vd. 30° cho countersink tuỳ chỉnh).
- **Tangent Chain** — như Fillet.
- **Corner Type** — cách xử lý điểm 3 cạnh gặp nhau (`Miter` / `Patch`). Nếu chamfer bị lỗi ở góc, thử đổi option này trước khi nghĩ đến việc đổi kích thước.

**Chamfer vs Fillet — chọn cái nào:**

| Tình huống | Chọn |
|---|---|
| Góc trong chịu lực | Fillet |
| Mép ngoài cầm tay | Fillet (êm tay hơn) |
| Cạnh đáy in 3D | **Chamfer** (in được không cần support) |
| Miệng lỗ để lắp trục/pin | **Chamfer** (dẫn hướng) |
| Cạnh trên cùng của lỗ in 3D | **Chamfer 0.5 mm** (bù cho phần over-extrusion) |
| Ưu tiên tốc độ tính toán | Chamfer (nhẹ hơn fillet nhiều) |

**Tip in 3D quan trọng:** chamfer **0.4–0.6 mm ở toàn bộ cạnh đáy** là kỹ thuật chuẩn để triệt elephant foot (phần chân bị phình do lớp đầu bị ép). Vừa in được hoàn hảo (45° không cần support), vừa giúp chi tiết ngồi phẳng khít khi lắp.

---

## 4. Shell

**Ý nghĩa:** Rỗng hoá một body đặc, giữ lại thành có độ dày cho trước. Đây là feature **quan trọng nhất** cho việc làm vỏ hộp.

**Cách dùng:**
1. Shell → chọn **face** cần bị bỏ đi (hở ra), hoặc chọn **body** để tạo khối rỗng kín hoàn toàn.
2. Nhập Thickness.

**Các tham số:**
- **Direction:**
  - `Inside` (default) — kích thước ngoài giữ nguyên, ăn vào trong. Dùng cho vỏ hộp (bạn cần kích thước ngoài đúng).
  - `Outside` — kích thước trong giữ nguyên, thêm ra ngoài. Dùng khi khoang trong phải khớp chính xác với board/pin (đây thường là trường hợp của bạn).
  - `Both` — chia đôi hai bên.
- **Tangent Chain** — chọn chuỗi face liền mạch.
- Có thể chọn **nhiều face** để bỏ (vd. bỏ mặt trên + mặt sau để có chỗ ra dây).

**Quy tắc thứ tự Timeline — điểm này rất hay sai:**

- **Fillet trước, Shell sau** → thành trong cũng bo theo, độ dày thành đều tuyệt đối. Đây là thứ tự bạn muốn 95% trường hợp.
- **Shell trước, Fillet sau** → chỉ cạnh ngoài được bo, chỗ góc thành sẽ dày hơn. Thường không mong muốn, và dễ fail.

**Khi Shell fail:** thành quá dày so với chi tiết nhỏ nhất của hình học (vd. body có fillet R1 mà bạn shell 2 mm → không đủ chỗ). Giảm thickness, hoặc chuyển Direction sang `Outside`.

**Tip cho vỏ ESP32 in trên K1C (nozzle 0.4 mm):** chọn thickness là **bội số nguyên của line width** để slicer sinh ra số wall loop tròn trịa, không có gap fill:
- `1.2 mm` = 3 walls — mỏng, chỉ cho vỏ trang trí.
- `1.6 mm` = 4 walls — điểm cân bằng tốt nhất, mặc định nên dùng.
- `2.0 mm` = 5 walls — cho vỏ cần cứng, có lỗ vít, hoặc chống nước.
- Chỗ có lỗ vít M3 self-tapping thì cần cục boss dày ≥ 2.5 mm quanh lỗ, đừng dựa vào thành shell.

---

## 5. Combine

**Ý nghĩa:** Boolean giữa các **body đã tồn tại**. Khác với `Operation: Cut` trong Extrude (dùng sketch để cắt), Combine dùng chính body làm dao cắt.

**Cách dùng:**
1. **Target Body** — chọn **đúng 1** body, là body sẽ bị biến đổi và tồn tại sau đó.
2. **Tool Bodies** — chọn 1 hoặc nhiều body làm "dao".
3. Chọn Operation.

**Ba Operation:**
- `Join` — hợp lại thành một body. Phần chồng lấn được merge, không bị nhân đôi vật liệu.
- `Cut` — Target trừ đi Tool. Đây là cách chuẩn để tạo hình dạng phức tạp: mô hình khoang trong như một body riêng rồi trừ nó ra khỏi vỏ.
- `Intersect` — chỉ giữ phần **giao nhau**. Thủ pháp cổ điển để tạo hình 3D từ hai profile khác hướng (vd. extrude side view + extrude top view → intersect → được hình có cả hai đường cong).

**Hai checkbox quan trọng:**
- **Keep Tools** ⚠️ — mặc định *tắt*, tức là tool body bị tiêu thụ mất. **Luôn bật** nếu bạn còn cần dùng tool đó lần nữa (vd. cắt cùng một khoang ra khỏi cả nắp và cả thân hộp). Rất nhiều người phải undo vì quên cái này.
- **New Component** — kết quả tạo thành component mới. Chỉ dùng khi bạn thực sự cần quản lý assembly.

**Pattern làm việc rất hữu ích:** tạo một body đại diện đúng kích thước vật thật (board ESP32-C3, pin 18650, đầu USB-C, module HLK-LD2410S) → gọi là *tool body* → `Combine → Cut → Keep Tools` để khoét khoang. Ưu điểm: khi bạn muốn nới clearance, chỉ cần `Offset Face` cái tool body lên 0.3 mm, mọi khoang tự cập nhật. Đây gọi là modeling bằng negative space.

---

## 6. Split Body

**Ý nghĩa:** Chia một body thành nhiều body bằng một mặt/đường cắt. Không mất vật liệu, chỉ tách.

**Cách dùng:**
1. **Body to Split** — chọn body (chọn được nhiều).
2. **Splitting Tool** — có thể là:
   - Construction Plane hoặc plane của Origin,
   - một **face** phẳng hoặc cong của body khác,
   - một **surface body**,
   - một **sketch curve** (line, spline, arc) — cắt theo phương vuông góc với mặt sketch.
3. **Extend Splitting Tool** — bật nếu công cụ cắt không đủ dài/rộng để xuyên hết body, Fusion sẽ tự kéo dài về mặt toán học.

**Sau khi split:** bạn được N body trong folder Bodies, tên `Body1`, `Body2`… **Đổi tên ngay** (`Bottom`, `Lid`), vì sau này chọn sai body là chuyện thường.

**Ứng dụng chính:**
- **Chia hộp thành thân + nắp.** Đây là cách đúng: mô hình toàn bộ hộp như một khối liền, fillet/shell xong, rồi mới Split. Như vậy hai nửa khớp nhau hoàn hảo theo định nghĩa.
- **Chia chi tiết to vượt build volume.** K1C của bạn có khổ in 220×220×250 mm — chi tiết dài hơn thì split rồi in từng phần. Sau khi split, nhớ thêm **lỗ dowel pin** (Ø3–4 mm, sâu 8 mm, clearance +0.2 mm) hoặc rãnh ghép ở mặt cắt để định vị khi dán.
- **Tạo mặt cắt để chụp hình minh hoạ** (split rồi ẩn một nửa).

**Phân biệt với hai feature liên quan:**
- `Split Face` — chỉ chia *bề mặt* thành nhiều vùng, body vẫn nguyên một khối. Dùng để gán 2 màu khác nhau lên cùng một body, hoặc tạo vùng ranh giới cho pattern/emboss.
- `Combine → Cut` — mất vật liệu; Split Body thì không.

---

## 7. Move/Copy (`M`)

**Ý nghĩa:** Dịch chuyển hoặc nhân bản đối tượng. Nghe đơn giản, nhưng đây là feature **dễ phá vỡ parametric nhất** trong nhóm Modify.

**Move Object — bạn chọn loại gì:**
- `Bodies` — an toàn, chỉ dịch cả khối.
- `Components` — có ứng xử riêng, xem lưu ý bên dưới.
- `Faces` — **direct editing**, cẩn thận.
- `Sketch Objects` — dịch entity trong sketch.

**Move Type:**
- `Free Move` — hiện gizmo đủ 6 bậc tự do (3 mũi tên translate, 3 cung rotate, các mặt phẳng). Trực quan nhưng khó chính xác.
- `Translate` — nhập X / Y / Z. Chính xác, nên dùng.
- `Rotate` — chọn axis + nhập góc.
- `Point to Point` — chọn điểm nguồn và điểm đích. Cực nhanh để canh thẳng hai chi tiết theo tâm lỗ.
- **Set Pivot** — đổi gốc của gizmo trước khi biến đổi.

**Create Copy** — checkbox tạo bản sao thay vì dịch bản gốc. Lưu ý: copy này là **bản sao độc lập**, sửa bản gốc thì bản copy *không* đổi theo. Nếu muốn liên kết, dùng `Pattern` hoặc `Mirror` (hai feature này parametric, nên ưu tiên hơn Move/Copy).

**Ba lưu ý quan trọng:**

1. **Move trên Face là direct edit.** Nó không liên kết với sketch nào cả. Sau này bạn sửa sketch gốc thì Move này có thể mất tham chiếu và báo lỗi (dấu chấm than vàng trên Timeline). Nếu vị trí là một *ý định thiết kế* → hãy đặt dimension trong sketch, không dùng Move.

2. **Move trên Component:** nếu component đã có Joint, Fusion sẽ hỏi *Capture Position* hay không. Với assembly, cách đúng là dùng `Assemble → Joint` / `Align`, không dùng Move — vì Joint mô tả *quan hệ*, còn Move chỉ ghi một vị trí chết.

3. **Move không phải cách để canh vị trí ban đầu.** Nếu bạn thấy mình phải Move body ngay sau khi tạo nó, nghĩa là sketch đặt sai chỗ. Sửa sketch sẽ cho mô hình bền hơn nhiều.

---

## 8. Scale

**Ý nghĩa:** Phóng to / thu nhỏ đối tượng. Đơn giản nhất về mặt vận hành, nhưng **nguy hiểm nhất** về mặt thiết kế.

**Các tham số:**
- **Entities** — Body, Component, hoặc Sketch.
- **Point** — điểm cố định khi scale (bắt buộc chọn). Chọn Origin nếu muốn giữ gốc toạ độ; chọn một vertex nếu muốn góc đó bất động.
- **Scale Type:**
  - `Uniform` — một hệ số cho cả 3 trục.
  - `Non Uniform` — X / Y / Z riêng. **Không áp dụng được cho Component**, chỉ cho Body và Sketch.

**Vì sao phải cẩn thận:**

Scale nhân *tất cả* kích thước, kể cả những kích thước **không được phép đổi**:
- Lỗ M3 (Ø3.4) × 1.05 → Ø3.57 mm → không còn là lỗ M3 nữa.
- Ren M8×1.25 sau scale → bước ren 1.31 mm → không ăn với bất kỳ bulông nào trên đời.
- Rãnh O-ring, khe cắm USB-C, khoang chứa pin 18650 → tất cả lệch khỏi kích thước vật thật.
- Độ dày thành cũng đổi → không còn là bội số của line width.

Nói cách khác: **các kích thước có ràng buộc với thế giới bên ngoài không được scale**, mà scale thì không phân biệt được cái nào là cái nào.

**Khi nào dùng Scale hợp lý:**
- **Bù co ngót vật liệu.** Nếu bạn đo được ABS co 0.6%, scale 1.006. Nhưng thường nên làm ở slicer, không ở CAD.
- Xử lý body import từ mesh/STL sai đơn vị (vd. bị lệch 25.4×).
- Prototype thử tỉ lệ nhanh, biết trước là sẽ bỏ đi.
- Non Uniform trên **sketch** để nắn nhanh một profile trang trí (không ràng buộc kích thước).

**Nguyên tắc nên theo:** giữ CAD ở kích thước nominal đúng. Cần scale để in thì làm trong **Creality Print** (scale ở slicer không phá mô hình gốc, và bạn revert được bất cứ lúc nào). Nếu bạn thấy mình cần scale trong Fusion để chi tiết vừa khít, dấu hiệu thật là cần sửa **clearance** — mà việc đó thuộc `Offset Face`, không phải Scale.

---

## Tổng kết & thứ tự Timeline khuyến nghị

Với một vỏ hộp điện tử điển hình, thứ tự này cho kết quả bền vững nhất:

```
1. Sketch biên dạng ngoài
2. Extrude thành khối đặc
3. Tạo tool bodies (board, pin, connector) — kích thước vật thật + clearance
4. Fillet / Chamfer cạnh ngoài
5. Shell (Outside) → tạo thành
6. Combine → Cut (Keep Tools ON) → khoét khoang, cửa connector
7. Hole → lỗ vít + boss
8. Split Body → tách thân / nắp
9. Chamfer 0.5 mm cạnh đáy của từng nửa (chống elephant foot)
```

Bảng tra nhanh:

| Bạn muốn | Dùng |
|---|---|
| Sửa nhanh mọi thứ bằng một shortcut | Press/Pull (`Q`) |
| Nới lỗ / làm dày thành sau khi đã in thử | Press/Pull → Offset Face |
| Bo góc trong chịu lực | Fillet |
| Vát cạnh đáy để in đẹp | Chamfer 0.5 mm |
| Rỗng hoá vỏ hộp | Shell (Outside, 1.6 mm) |
| Khoét khoang theo hình vật thật | Combine → Cut + Keep Tools |
| Tách thân/nắp, chia chi tiết quá khổ | Split Body |
| Gán 2 màu lên cùng body | Split **Face** (không phải Split Body) |
| Nhân bản có liên kết | Pattern / Mirror (**không** phải Move/Copy) |
| Bù co ngót khi in | Scale ở slicer, không ở CAD |

Nếu bạn muốn, tôi có thể dựng một ví dụ hoàn chỉnh theo đúng 9 bước trên cho vỏ đèn cầu thang ESP32-C3 + 18650 của bạn — kèm số đo cụ thể cho từng bước và giải thích tại sao chọn con số đó.
