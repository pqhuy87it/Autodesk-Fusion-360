Dưới đây là giải thích chi tiết từng feature trong menu **Create** của Fusion 360 (Design → Solid → Create).

Trước tiên, chia nhóm để dễ nhớ:

| Nhóm | Feature | Cần gì làm input |
|---|---|---|
| Sketch-based | Extrude, Revolve, Coil | Profile 2D kín (+ axis/path) |
| Path-based | Pipe | Đường path (sketch line/spline/edge) |
| Face-based | Hole, Thread | Mặt phẳng hoặc mặt trụ có sẵn |
| Primitive | Box, Cylinder, Sphere, Torus | Chỉ cần chọn plane + nhập số |

Điểm chung: mọi feature đều có tham số **Operation** — `New Body` (body mới), `Join` (hợp), `Cut` (trừ), `Intersect` (giao), `New Component`. Đây là chỗ hay bị bỏ qua nhất, nhưng nó quyết định feature của bạn *thêm* hay *khoét* vật liệu.

---

## 1. Extrude (`E`)

**Ý nghĩa:** Đẩy một profile 2D theo phương pháp tuyến của mặt sketch để tạo khối 3D. Đây là feature dùng nhiều nhất, khoảng 70% mô hình cơ khí chỉ cần Extrude + Fillet.

**Cách dùng:**
1. Vẽ sketch profile **kín** (nếu hở, Fusion chỉ tạo được surface).
2. `E` → chọn profile (có thể chọn nhiều profile cùng lúc).
3. Nhập Distance → Enter.

**Các option quan trọng:**
- **Direction:** `One Side` / `Two Sides` (mỗi chiều một khoảng khác nhau) / `Symmetric` (đối xứng — chú ý có checkbox *Measurement*: Whole Length hay Half Length).
- **Extent Type:**
  - `Distance` — khoảng cố định.
  - `To Object` — đẩy đến khi chạm một face/plane/point. Rất mạnh vì nó *liên kết*: sửa object kia thì extrude tự cập nhật.
  - `All` — xuyên hết mọi thứ. Dùng khi Cut lỗ mà không muốn lo chiều dày sẽ đổi.
- **Start:** `Profile Plane` / `Offset` (bắt đầu cách sketch một khoảng) / `Object`. Option `Offset` giúp bạn không phải tạo thêm construction plane.
- **Taper Angle:** vát nghiêng thành. Với in 3D FDM, taper âm nhẹ ở mặt dưới giúp giảm support; taper dương tạo draft angle cho khuôn.

**Tip:** `Q` (Press Pull) là phiên bản nhanh — chọn face rồi kéo, Fusion tự hiểu là Extrude hay Offset Face.

---

## 2. Revolve

**Ý nghĩa:** Quay profile quanh một trục để tạo khối tròn xoay. Bất cứ vật gì đối xứng trục (ly, chai, trục, puly, nắp vặn, thân loa) đều nên làm bằng Revolve thay vì Extrude.

**Cách dùng:**
1. Vẽ **một nửa** mặt cắt của vật thể, cùng với một `Line` đặt thành **Construction** (dấu `X` hoặc phím `X`) làm trục.
2. Revolve → Profile: chọn profile → Axis: chọn line đó (hoặc chọn thẳng trục X/Y/Z trong Origin).
3. Angle: `Full` (360°) hoặc nhập góc.

**Lưu ý:**
- Axis **phải đồng phẳng** với profile và **không được cắt qua** profile, nếu không sẽ báo lỗi self-intersection.
- Nên dùng luôn trục Origin làm axis khi có thể → mô hình bền vững hơn khi edit về sau.
- Revolve góc < 360° + Operation `Cut` là cách nhanh nhất để khoét rãnh vòng, khe hở quạt.

---

## 3. Hole

**Ý nghĩa:** Tạo lỗ *có metadata*. Về hình học, nó tương đương "vẽ circle rồi Extrude Cut", nhưng Hole lưu thông tin lỗ là loại gì, ren gì, cho fastener nào — nên khi xuất Drawing hay sang CAM, Fusion tự tạo hole table, tự chọn dao. Với in 3D thì lợi ích chính là **dễ sửa và đọc lại ý định thiết kế**.

**Cách dùng:**
1. Chọn một face phẳng → Hole. Fusion tự tạo point, bạn kéo/gán dimension để định vị. Hoặc dùng **Placement: From Sketch** rồi chọn các sketch point có sẵn (cách này parametric hơn, nên ưu tiên).
2. `Concentric` — chọn face + một edge tròn để lỗ tự đồng tâm.

**Các tham số:**
- **Hole Type:** `Simple` / `Counterbore` (lỗ bậc cho đầu vít chìm trụ) / `Countersink` (vát nón cho vít đầu côn) / `Counterdrill`.
- **Hole Tap Type:**
  - `Simple` — chỉ đường kính bạn nhập.
  - `Clearance` — chọn Fastener Type (vd. Socket Head Cap Screw), Size (M3), Fit (Close/Normal/Loose) → Fusion tự tính đường kính theo chuẩn.
  - `Tapped` — lỗ ren, chọn designation M3×0.5. Có checkbox `Modeled` giống Thread.
- **Drill Point:** `Flat` hoặc `Angle` (118°/135° — mô phỏng mũi khoan thật).
- **Extent:** Distance / To / All.

**Tip cho in 3D (K1C):** lỗ in ra luôn *nhỏ hơn* nominal do co ngót và over-extrusion ở chu vi trong. Với lỗ clearance M3, đặt 3.4–3.6 mm thay vì 3.2 mm. Lỗ đứng (trục Z) sai lệch nhiều hơn lỗ ngang.

---

## 4. Pipe

**Ý nghĩa:** Quét (sweep) một mặt cắt chuẩn dọc theo một đường path. Là bản đơn giản hoá của Sweep, chuyên cho ống/thanh/dây.

**Cách dùng:**
1. Vẽ path: sketch 2D, **3D Sketch** (bật checkbox 3D Sketch trong sketch palette), hoặc chọn thẳng edge của body có sẵn.
2. Pipe → chọn path → cấu hình section.

**Các tham số:**
- **Distance:** 0–1, tỉ lệ chiều dài path được tạo ống (dùng để tạo ống chạy dở, hoặc animate).
- **Section:** `Circular` / `Square` / `Triangular`.
- **Section Size:** đường kính / cạnh.
- **Section Position:** `Path Center` / `Path Inside` / `Path Outside` — quyết định path là tâm ống hay mép ống.
- **Hollow + Thickness:** tạo ống rỗng trực tiếp, không cần Shell.

**Lưu ý:** path có góc nhọn sẽ làm Pipe fail. Luôn `Fillet` các góc trong sketch trước, với bán kính ≥ bán kính ống. Ứng dụng hay: khung tube, tay cầm, đi dây cable trong vỏ hộp, đường ống nước.

---

## 5–8. Primitives: Box, Cylinder, Sphere, Torus

Bốn feature này đều làm cùng một việc: **tự tạo sketch + tự extrude/revolve** trong một bước. Chúng vẫn hoàn toàn parametric — sau đó bạn vẫn edit được cả sketch lẫn feature trong Timeline. Dùng khi cần khối cơ bản nhanh, hoặc khi cần một hình dạng để `Cut`.

### Box
Chọn plane → click 2 điểm tạo rectangle → nhập Height.
Tham số: Length, Width, Height, Direction.
→ Nhanh hơn Extrude khi tạo khối chữ nhật thô, nhưng khi muốn kiểm soát vị trí chính xác thì vẫn nên vẽ sketch có dimension rõ ràng.

### Cylinder
Chọn plane → click tâm → kéo bán kính → nhập Height.
Tham số: Diameter, Height, Direction (One Side/Two Sides/Symmetric).
→ Hữu ích nhất khi dùng `Cut` để khoét trụ, hoặc tạo boss/pin nhanh.

### Sphere
Chọn plane → click tâm → nhập Diameter.
→ Ít dùng trong cơ khí, nhưng tiện cho ball joint, chân đế bi, hoặc `Cut` để tạo hốc cầu (vd. lõm ngón tay bấm).

### Torus
Chọn plane → click tâm → định đường kính vòng → nhập đường kính ống.
Tham số:
- **Torus Diameter** — đường kính vòng lớn.
- **Torus Position:** `Inside` / `On Path` / `Outside` — quy chiếu vòng tròn bạn vừa vẽ so với thân torus.
- **Diameter / Section Size** — đường kính tiết diện ống.

→ Ứng dụng thực tế mạnh nhất: **tạo rãnh O-ring**. Tạo Torus đúng kích thước O-ring, Operation `Cut`, xong. Nhanh hơn Revolve nhiều. Ngoài ra dùng cho vòng tay cầm, viền tròn trang trí.

---

## 9. Coil

**Ý nghĩa:** Tạo hình xoắn ốc dạng khối — lò xo, ren tuỳ chỉnh, trục vít tải liệu, ruột thang xoắn.

**Cách dùng:**
1. Chọn plane → click tâm → định đường kính coil (đây là đường kính **đường tâm** của dây, không phải đường kính ngoài).
2. Cấu hình dialog.

**Các tham số:**
- **Type** (quyết định 2 trong 3 biến còn lại được nhập, biến thứ 3 tự tính):
  - `Revolution and Height` — biết số vòng và tổng chiều cao.
  - `Revolution and Pitch` — biết số vòng và bước.
  - `Height and Pitch` — biết chiều cao và bước.
  - `Spiral` — xoắn phẳng (như lò xo đồng hồ, hình đĩa xoáy).
- **Diameter, Revolutions, Height, Pitch.**
- **Angle** — taper, làm coil hình nón (lò xo côn).
- **Section:** Circular / Square / Triangular + **Section Size** + **Section Position** (Inside/Outside/Center — cực quan trọng khi bạn muốn coil khớp chính xác với đường kính trụ).
- Nút đảo chiều xoắn (phải/trái tay).

**Lưu ý:**
- Coil rất "nặng" về tính toán. Trên 30–40 revolutions với section phức tạp là bắt đầu chậm. Đặt Coil **muộn** trong timeline nếu được.
- Muốn ren tuỳ chỉnh (trapezoidal, ren bước lớn cho in 3D) mà `Thread` không có: dùng Coil + Operation `Cut` trên trụ. Đây là cách chuẩn để làm ren in được đẹp.
- In lò xo thật: TPU (95A), 100% infill, in dựng đứng.

---

## 10. Thread

**Ý nghĩa:** Áp ren lên một **mặt trụ có sẵn** (trụ ngoài → ren ngoài; lỗ trụ → ren trong). Bạn không tạo hình học mới, chỉ "gắn ren" lên mặt đã có.

**Cách dùng:**
1. Thread → chọn cylindrical face.
2. Fusion tự đề xuất size gần nhất với đường kính trụ.

**Các tham số:**
- **Modeled** ⚠️ — checkbox quan trọng nhất.
  - Bỏ tick (default): ren chỉ là **cosmetic**, hiển thị dạng texture. File nhẹ, viewport nhanh, đủ cho Drawing và CAM. **Nhưng export STL sẽ KHÔNG có ren.**
  - Tick: tạo geometry ren thật. Bắt buộc nếu bạn in 3D.
- **Full Length** — bỏ tick để nhập Length + Offset (ren chỉ một đoạn, có phần chân trơn).
- **Thread Type:** ISO Metric profile, ANSI Unified, NPT (ren ống côn), BSP…
- **Size / Designation / Class:** vd. Size 8mm → Designation `M8x1.25` → Class `6g`.
- **Direction:** Right hand / Left hand.

**Tip quan trọng khi in 3D:** Fusion tạo ren theo đúng chuẩn, **không có tham số tolerance**. Ren in ra sẽ quá chặt hoặc kẹt hoàn toàn. Cách xử lý:
- Ren nhỏ hơn M8 gần như không in được ra ren dùng được trên FDM. Ưu tiên M10+ hoặc ren tự thiết kế bước ≥ 2 mm.
- Với ren trong (lỗ): tạo lỗ lớn hơn nominal ~0.3–0.4 mm rồi mới apply Thread.
- Hoặc sau khi tạo Thread (Modeled), dùng `Modify → Offset Face` chọn các mặt ren và offset −0.15 mm để tạo khe hở.
- Tăng số Wall loops (≥3) và giảm speed ở ngoài chu vi để profile ren nét hơn.

---

## Tổng kết chọn feature

| Bạn muốn | Dùng |
|---|---|
| Khối từ mặt cắt phẳng | Extrude |
| Vật đối xứng trục | Revolve |
| Lỗ vít có chuẩn | Hole |
| Ống/thanh theo đường cong | Pipe |
| Khối cơ bản nhanh / khối để Cut | Box, Cylinder, Sphere |
| Rãnh O-ring | Torus + Cut |
| Lò xo, ren tuỳ chỉnh | Coil |
| Ren chuẩn ISO/ANSI | Thread (nhớ tick Modeled) |

Nếu bạn muốn, tôi có thể đi sâu vào một feature cụ thể kèm ví dụ thực tế — ví dụ làm nắp vặn (Thread + Revolve) hay hộp có rãnh O-ring chống nước cho project ESP32 của bạn.
