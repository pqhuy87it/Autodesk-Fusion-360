Tiếp phần **Assemble**. Đây là nhóm có tư duy khác hẳn hai nhóm trước: Create/Modify làm việc với **hình học**, còn Assemble làm việc với **quan hệ giữa các chi tiết**. Nếu bạn từng thấy mô hình "chạy" được trong Fusion (nắp mở ra, trục quay, con trượt di chuyển) thì đó là nhóm này.

Bức tranh tổng thể:

| Feature | Trả lời câu hỏi |
|---|---|
| New Component | "Chi tiết này là một *thực thể riêng*" |
| Joint | "Đặt nó vào đúng chỗ, và cho phép nó chuyển động thế nào" |
| As-Built Joint | "Nó đã đúng chỗ rồi, chỉ cần khai báo chuyển động" |
| Rigid Group | "Cả đám này dính chặt vào nhau, đừng bắt tôi tạo joint từng cái" |
| Joint Limit | "Bản lề chỉ được mở tới 100°, không hơn" |

---

## Trước hết: Body vs Component

Đây là khái niệm nền tảng, hiểu sai chỗ này thì toàn bộ nhóm Assemble sẽ vô nghĩa.

| | Body | Component |
|---|---|---|
| Bản chất | Một khối hình học | Một **container** |
| Chứa được gì | Chỉ là hình học | Bodies, Sketches, Construction geometry, Joints, Components khác |
| Có Origin riêng | Không | **Có** (nên di chuyển được độc lập) |
| Gắn Joint được | **Không** | Có |
| Xuất hiện trong BOM / Drawing parts list | Không | Có |
| Nhân bản có liên kết | Không | Có (occurrence) |
| Xuất được thành file riêng | Không trực tiếp | Có |

Nguyên tắc thực dụng: **một vật thể được sản xuất riêng lẻ thì phải là một Component.** Vỏ dưới, nắp, ốc vít, board ESP32, viên pin 18650 — mỗi thứ một component. Còn body chỉ nên là "vật liệu" bên trong component đó, hoặc là tool body dùng để cắt.

---

## 1. New Component

**Ý nghĩa:** Tạo một component rỗng, hoặc bọc body có sẵn thành component.

**Các tham số trong dialog:**

- **Type:** `Standard` (thường) / `Sheet Metal` (kèm rule dập tôn) / `Harness` (đi dây, bản mới).
- **From Bodies** — checkbox cực hữu ích: chọn body đã có, Fusion tạo component và **đưa body đó vào trong**. Đây là cách sửa sai khi bạn đã mô hình xong mọi thứ dưới dạng body rồi mới nhận ra cần assembly.
- **Internal / External:**
  - `Internal` (default) — component nằm trong chính file này.
  - `External` — Fusion tạo **một file riêng** trong project và link vào. Sửa file đó thì mọi design đang dùng nó đều cập nhật.
- **Name** — đặt tên ngay tại đây. Đừng để `Component1`.
- **Activate** — xem mục dưới.

**Khái niệm Activate (quan trọng nhất):**

Bên cạnh mỗi component trong Browser có một **dấu tròn nhỏ** (radio). Click vào đó = "activate" component. Khi một component đang active:
- Mọi sketch, body, feature bạn tạo mới sẽ **nằm bên trong** component đó.
- Timeline chuyển sang timeline riêng của component (bạn sẽ thấy nó ngắn hơn).
- Các component khác bị mờ đi.

Quên activate là lỗi phổ biến nhất: bạn tưởng đang vẽ nắp hộp, thực ra đang vẽ ở root, kết quả là body nằm lơ lửng ngoài mọi component. Click vào tên file ở đỉnh Browser để quay về root.

**Occurrence vs Definition — chỗ dễ nhầm:**

| Cách nhân bản | Kết quả |
|---|---|
| `Ctrl+C` → `Ctrl+V` | **Occurrence mới**, dùng chung definition. Sửa một cái → tất cả đổi theo. |
| `Ctrl+C` → **Paste New** | Copy **độc lập**, có definition riêng. Sửa không ảnh hưởng bản gốc. |
| `Move/Copy` với Create Copy | Độc lập |
| `Pattern` / `Mirror` component | Occurrence, có liên kết parametric |

Với 4 con ốc M3 giống nhau: dùng `Ctrl+V` hoặc Pattern → sửa một cái là sửa cả bốn.

**Ground:** Right-click component → **Ground** (biểu tượng đinh ghim). Component bị ground thì bất động tuyệt đối. **Luôn ground chi tiết nền** (thân hộp, base plate) trước khi tạo joint — nếu không, khi bạn kéo thử chuyển động, cả assembly sẽ "bay" đi thay vì chỉ chi tiết cần quay.

---

## 2. Joint (`J`)

**Ý nghĩa:** Định nghĩa đồng thời **hai** thứ: vị trí lắp và bậc tự do chuyển động.

**Cách dùng:**
1. `J` → dialog mở ra, ở tab **Motion**.
2. **Component 1** — chọn một điểm trên chi tiết **sẽ di chuyển**.
3. **Component 2** — chọn điểm trên chi tiết **đứng yên**.
4. Fusion lôi Component 1 tới khớp với Component 2.
5. Chọn **Type**.

⚠️ **Thứ tự chọn quyết định cái nào bị dịch chuyển.** Chọn ngược là lý do vỏ hộp của bạn nhảy sang chỗ khác thay vì cái nắp.

**Joint Origin — snap point:**

Khi bạn hover chuột, Fusion tự đề xuất điểm bắt. Các loại snap:

| Hover vào | Điểm bắt được |
|---|---|
| Mặt phẳng | Tâm mặt (nếu là hình chữ nhật/tròn) |
| Cạnh tròn | **Tâm đường tròn** — dùng nhiều nhất, để canh lỗ vít, trục |
| Cạnh thẳng | Điểm giữa hoặc hai đầu |
| Vertex | Chính điểm đó |

Nếu điểm bạn cần không có sẵn, dùng `Assemble → Joint Origin` để tạo một joint origin tuỳ ý (offset, đặt trên construction plane), rồi joint vào nó. Đây là cách xử lý sạch nhất cho các vị trí lệch tâm.

**Bảy Joint Type:**

| Type | DOF | Chuyển động | Ví dụ thực tế |
|---|---|---|---|
| **Rigid** | 0 | Không | Ốc vít đã siết, chi tiết dán cố định |
| **Revolute** | 1 quay | Quay quanh 1 trục | Bản lề nắp hộp, bánh xe, trục motor |
| **Slider** | 1 trượt | Trượt theo 1 trục | Ngăn kéo, nắp trượt, khay pin |
| **Cylindrical** | 1 quay + 1 trượt | Quay và trượt cùng trục | Bulông đang vặn vào, piston có xoay |
| **Pin-Slot** | 1 quay + 1 trượt | Quay quanh trục A, trượt theo trục **B khác** | Cơ cấu cam, chốt chạy trong rãnh |
| **Planar** | 2 trượt + 1 quay | Tự do trên một mặt phẳng | Vật đặt trên bàn, chuột máy tính |
| **Ball** | 3 quay | Quay mọi hướng, tâm cố định | Khớp cầu, chân camera, ball joint |

Sau khi chọn Type, dialog hiện thêm ô để chỉ định **trục** (Rotate/Slide axis: X, Y, Z hoặc Custom).

**Tab Position:**
- **Angle** — quay Component 1 quanh trục joint trước khi cố định.
- **Offset X / Y / Z** — dịch một khoảng. Đây là chỗ bạn tạo **khe hở lắp ghép**: joint hai mặt vào nhau rồi offset Z 0.2 mm.
- **Flip** — đảo hướng 180°. Rất hay cần khi nắp bị úp ngược.

**Kiểm tra chuyển động:** giữ chuột kéo trực tiếp component → nó di chuyển trong giới hạn joint cho phép. Hoặc right-click joint → **Animate Joint**.

⚠️ **Joint không có va chạm.** Fusion mặc định cho phép chi tiết xuyên qua nhau. Muốn phát hiện đụng độ thật, phải bật `Assemble → Enable Contact Sets` (hoặc Enable All Contact). Đừng tin mắt mình khi nắp "đóng vừa khít" — Joint chỉ mô tả toán học.

---

## 3. As-Built Joint

**Ý nghĩa:** Giống Joint về mặt định nghĩa chuyển động, nhưng **không di chuyển gì cả** — nó chấp nhận vị trí hiện tại là đúng và chỉ khai báo quan hệ.

**Cách dùng:**
1. Chọn Component 1, Component 2 (thứ tự không quan trọng ở đây, vì không có gì bị dịch).
2. Chọn Type.
3. Nếu là Revolute/Slider/Cylindrical/Pin-Slot: chọn thêm **Geometry** — một cạnh tròn, một mặt trụ, hoặc một điểm để xác định trục quay/trượt.

**Khi nào dùng cái này thay vì Joint:**

Đây là feature bạn sẽ dùng nhiều hơn Joint nếu bạn theo cách làm **top-down** — tức mô hình toàn bộ sản phẩm như một khối liền tại đúng vị trí, rồi mới tách ra component. Cách bạn đang làm hộp có mặt cắt chéo, Split Body thành hai nửa chính là top-down: hai nửa **đã** nằm đúng chỗ, không cần Joint lôi chúng đi đâu — chỉ cần As-Built Joint khai báo "hai cái này Rigid" hoặc "nửa trên quay quanh cạnh này".

| | Joint | As-Built Joint |
|---|---|---|
| Có dịch chuyển component | **Có** (Comp 1 bay tới Comp 2) | Không |
| Cần chọn joint origin | Có (2 điểm) | Không (chỉ cần geometry cho trục) |
| Có tab Position / Offset | Có | Không |
| Phù hợp với | Bottom-up (chi tiết làm rời rồi lắp) | Top-down (mô hình tại chỗ) |
| Import từ file ngoài | Dùng Joint | — |

**Tip:** nếu đã dùng As-Built Joint mà sau đó cần offset, không có tab Position. Cách xử lý: `Move/Copy` component tới vị trí mới → right-click joint → **Capture Position**, hoặc xoá joint và làm lại bằng Joint thường.

---

## 4. Rigid Group

**Ý nghĩa:** Khoá một **nhóm** component lại thành một khối rắn duy nhất, chỉ bằng một feature.

**Cách dùng:**
1. Rigid Group → chọn nhiều component (Browser hoặc canvas).
2. **Include child components** — bật thì các component con bên trong cũng bị khoá theo.
3. OK.

**Vì sao cần nó khi đã có Rigid Joint:**

| | Rigid Joint | Rigid Group |
|---|---|---|
| Số component | Đúng 2 | Bao nhiêu cũng được |
| Có dịch chuyển | Có (Comp 1 di chuyển tới) | Không, giữ nguyên vị trí |
| Có offset/angle | Có | Không |
| Số feature cần tạo cho 8 chi tiết | 7 joint | **1 rigid group** |

Với vỏ hộp có 4 ốc + 2 nửa vỏ + board: dùng Rigid Joint là 6 feature riêng lẻ; Rigid Group là một feature, timeline gọn hơn, tính toán nhẹ hơn.

**Ứng dụng thực tế quan trọng:** Rigid Group **suppress được**. Right-click → Suppress. Nghĩa là bạn có thể:
- Rigid Group "nắp + tất cả linh kiện gắn trên nắp" → khi mở bản lề, cả cụm quay theo như một khối.
- Cần rời chúng ra để chỉnh sửa → suppress tạm, làm xong un-suppress.

Đây là cách chuẩn để mô hình một sub-assembly mà không phải tạo cấu trúc component lồng nhau phức tạp.

---

## 5. Joint Limit

**Ý nghĩa:** Giới hạn phạm vi chuyển động của một joint, và đặt vị trí nghỉ.

**Cách mở:** `Assemble → Joint Limits`, hoặc nhanh hơn: right-click joint trong Browser → **Edit Joint Limits**.

**Các tham số:**

- **Joint** — chọn joint cần giới hạn.
- **Rest** — vị trí nghỉ. Khi bạn kéo component rồi thả, hoặc bấm `Revert` trên toolbar, nó về đây. Ví dụ nắp hộp có Rest = 0° (đóng).
- **Minimum / Maximum** — mỗi cái có checkbox riêng, có thể chỉ giới hạn một đầu.
- **Animate** — bật để xem preview chuyển động ngay khi bạn nhập số.
- Với **Cylindrical** joint có cả hai bộ (góc quay và khoảng trượt) → hai nhóm Min/Max riêng.

**Áp dụng được cho joint nào:**

| Joint Type | Có Joint Limit |
|---|---|
| Revolute | Có (góc, độ) |
| Slider | Có (khoảng, mm) |
| Cylindrical | Có (cả góc **và** khoảng) |
| Pin-Slot | Có (cả hai) |
| Planar, Ball | Có (nhưng ít dùng, khó kiểm soát) |
| Rigid | Không (0 DOF) |

**Vì sao nên đặt Limit dù chỉ để xem cho vui:**

1. **Phát hiện lỗi thiết kế sớm.** Đặt Max = 100° cho bản lề, animate, thấy nắp đâm vào tường phía sau → biết ngay phải dịch tâm bản lề.
2. Bắt buộc nếu làm **Motion Study** hoặc muốn nắp không lật quá đà khi kéo thử.
3. Nó là **tài liệu thiết kế**: 6 tháng sau mở lại file, con số 100° cho bạn biết ý định ban đầu.

**Lưu ý:** Joint Limit là **kinematic thuần**, không có vật lý. Nó không biết gì về ma sát, lực, hay va chạm. Nếu bạn cần chi tiết dừng lại vì *đụng* nhau chứ không phải vì số bạn nhập, phải dùng Contact Sets.

---

## Áp dụng cho project của bạn

**Vỏ đèn cầu thang ESP32-C3 + 18650 + HLK-LD2410S:**

```
Cấu trúc component đề xuất:
├── Enclosure_Bottom      (Ground) ← luôn ground cái này
├── Enclosure_Lid
├── ESP32C3_Board         (external component, tái dùng)
├── Battery_18650
├── LD2410S_Module
└── Screw_M3x8 ×4         (Ctrl+V occurrence, không Paste New)

Joint cần tạo:
1. Bottom ← Ground
2. Lid → Bottom:           As-Built Joint, Revolute (nếu có bản lề)
                           hoặc Rigid nếu bắt vít
3. Joint Limit trên (2):   Rest 0°, Min 0°, Max 100°
4. Board → Bottom:         Joint, Rigid, snap tâm lỗ vít M2.5
5. Battery → Bottom:       Joint, Slider (mô phỏng lắp/tháo pin)
   + Joint Limit:          Min 0, Max 25 mm (khoảng cần rút pin ra)
6. LD2410S → Lid:          Joint, Rigid, snap tâm lỗ
7. Rigid Group:            Lid + LD2410S + ốc trên nắp
   → mở bản lề thì cả cụm quay theo
```

Cái Slider ở bước 5 rất đáng làm: nó trả lời câu hỏi thực tế "có đủ chỗ để rút pin ra không, hay phải tháo cả board?" — mà chỉ nhìn mô hình tĩnh thì không thấy.

**Hai lưu ý riêng cho in 3D:**

- **Joint không tạo clearance.** Bản lề print-in-place cần khe hở thật **0.3–0.4 mm** (K1C, nozzle 0.4) — phải mô hình bằng hình học, dùng `Offset Face` hoặc offset trong sketch. Revolute joint quay mượt trong Fusion không có nghĩa là in ra quay được.
- **Component = một lần in.** Khi export cho Creality Print, right-click từng component → `Save as Mesh` để có STL riêng, hoặc export cả assembly thành 3MF (giữ được nhiều object). Đừng export root thành một STL duy nhất, vì các chi tiết sẽ dính thành một mesh.

---

## Tổng kết

| Bạn muốn | Dùng |
|---|---|
| Biến body đã có thành chi tiết riêng | New Component → **From Bodies** |
| Tái dùng model board ESP32 ở nhiều project | New Component → **External** |
| Nhân 4 con ốc giống nhau, sửa 1 là sửa hết | `Ctrl+C` → `Ctrl+V` (occurrence) |
| Chi tiết nền không được xê dịch | Right-click → **Ground** |
| Lắp chi tiết vừa import vào đúng chỗ | **Joint** (có Position/Offset) |
| Khai báo chuyển động cho chi tiết đã đúng chỗ (sau Split Body) | **As-Built Joint** |
| Bản lề, trục quay | Joint **Revolute** |
| Khay pin, nắp trượt | Joint **Slider** |
| Khoá cả cụm 8 chi tiết bằng 1 feature | **Rigid Group** |
| Nắp chỉ mở tối đa 100° | **Joint Limit** trên revolute joint |
| Chi tiết không được xuyên qua nhau | **Enable Contact Sets** (không phải Joint Limit) |
| Khe hở lắp ghép thật để in được | Hình học (`Offset Face`), **không** phải Joint |

Còn hai nhóm nữa trong Solid tab là **Construct** (Offset Plane, Plane at Angle, Axis, Point…) và **Inspect** (Measure, Section Analysis, Interference). Nhóm Construct sẽ hữu ích trực tiếp cho cái hộp mặt cắt chéo bạn đang làm — `Plane at Angle` và `Plane Through Three Points` chính là công cụ để định nghĩa mặt cắt đó một cách parametric thay vì canh tay. Bạn muốn đi tiếp nhóm nào?
