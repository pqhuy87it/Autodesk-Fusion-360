Tiếp nhóm **Create nâng cao**. Nhóm này khác Extrude/Revolve ở một điểm cốt lõi: chúng đều **cần input phụ ngoài profile** — một path, một profile thứ hai, một face đích, hoặc một surface. Vì thế chúng mạnh hơn nhiều, nhưng cũng fail nhiều hơn, và gần như mọi lỗi đều đến từ input phụ đó chứ không phải từ profile.

Bức tranh tổng thể:

| Feature | Input | Cho ra | Thay thế cho |
|---|---|---|---|
| **Sweep** | Profile + Path | Khối theo đường dẫn | Pipe (bản mạnh hơn) |
| **Loft** | 2+ Profile (+ Rail/Centerline) | Khối chuyển tiếp giữa các mặt cắt | — |
| **Rib** | Sketch **hở** + body | Gân gia cường (blade) | Extrude mỏng thủ công |
| **Web** | Sketch **hở** + body | Vách trong lấp giữa các thành | Extrude + Combine |
| **Emboss** | Sketch kín + face (kể cả **cong**) | Chữ/hình nổi hoặc lõm | Extrude (chỉ được trên mặt phẳng) |
| **Thicken** | **Surface** | Solid có độ dày | Shell (theo hướng ngược) |

Chia theo mục đích thực tế:

- **Sweep, Loft, Thicken** → tạo *hình dạng* mà Extrude/Revolve không làm được.
- **Rib, Web** → tăng *độ bền* mà không tăng độ dày thành.
- **Emboss** → thêm *thông tin* lên bề mặt.

---

## 1. Sweep

**Ý nghĩa:** Quét một profile dọc theo một path. Bạn đã biết `Pipe` — Sweep chính là bản đầy đủ của nó: Pipe cho bạn chọn 3 tiết diện có sẵn, Sweep cho bạn **tiết diện tuỳ ý**.

**Cách dùng:**
1. Vẽ **path** (sketch 2D, 3D Sketch, hoặc chọn edge của body).
2. Vẽ **profile kín** trên một mặt phẳng — lý tưởng là mặt phẳng vuông góc với path tại điểm đầu.
3. Sweep → Profile → Path → nhập tham số.

### Ba Type

| Type | Input thêm | Dùng khi |
|---|---|---|
| **Single Path** | — | 90% trường hợp. Tiết diện không đổi |
| **Path + Guide Rail** | Một curve thứ hai | Tiết diện cần **thay đổi kích thước** dọc đường. Profile tự scale để bám theo rail |
| **Path + Guide Surface** | Một surface | Kiểm soát **hướng xoay** của profile theo pháp tuyến surface. Dùng cho gân chạy trên mặt cong |

Guide Rail là cái đáng học nhất trong ba: bạn có được một khối *phình ra rồi thu lại* mà không phải Loft nhiều profile. Ví dụ tay cầm to ở giữa, nhỏ ở hai đầu.

### Các tham số

- **Distance** (0–1) — tỉ lệ chiều dài path được quét. `1` là hết. Dùng < 1 để tạo phần quét dở.
- **Orientation** ⚠️ — tham số hay bị bỏ qua nhưng quan trọng:
  - `Perpendicular` (mặc định) — profile luôn **vuông góc với path**. Đây là sweep "thật", giống ống nước uốn.
  - `Parallel` — profile luôn **song song với mặt phẳng gốc** của nó. Kết quả giống như bị "xén nghiêng". Dùng khi tiết diện phải giữ nguyên hướng bất kể path đi đâu — ví dụ tay nắm cầu thang phải luôn nằm ngang, hoặc gân phải luôn thẳng đứng theo trục in.
- **Twist Angle** — xoay profile dọc đường. Tạo mũi khoan, cánh xoắn, chi tiết trang trí xoắn.
- **Taper Angle** — scale profile dọc đường (thu nhỏ/phình dần).
- **Chain Selection** — bật thì chọn một edge sẽ tự lấy cả chuỗi edge tiếp tuyến liền mạch. Tắt khi bạn chỉ muốn một đoạn.
- **Operation** — New Body / Join / Cut / Intersect.

### Khi Sweep fail — ba nguyên nhân, theo thứ tự phổ biến

**1. Path có góc nhọn.** Đây là nguyên nhân số một. Fillet mọi góc trong sketch path với **R ≥ nửa chiều rộng profile**. Góc vuông thì Sweep không có cách nào quét qua mà không tự cắt chính nó.

**2. Bán kính path nhỏ hơn profile.** Path uốn R3 mà profile là hình tròn Ø8 → mặt trong của khối tự chồng lấn. Toán học không giải được, không phải lỗi phần mềm. Tăng R hoặc giảm profile.

**3. Profile không vuông góc với path tại điểm đầu.** Fusion vẫn chạy nhưng cho hình méo ở đoạn đầu. Cách làm sạch: dùng `Construct → Plane Along Path` với tỉ lệ 0 để có mặt phẳng vuông góc chính xác tại điểm đầu, vẽ profile trên đó.

### Sweep vs Pipe

| | Pipe | Sweep |
|---|---|---|
| Tiết diện | Circular / Square / Triangular | **Bất kỳ** |
| Rỗng trực tiếp | Có (Hollow + Thickness) | Không (phải Shell hoặc vẽ profile rỗng sẵn) |
| Guide rail | Không | Có |
| Twist | Không | Có |
| Số bước | 1 (chỉ cần path) | 2 (path + profile) |

Nguyên tắc: tiết diện tròn/vuông đơn giản → **Pipe** (nhanh hơn). Tiết diện đặc thù → **Sweep**.

### Ứng dụng cho project của bạn

**Rãnh gasket trên mặt cắt chéo** — đây là ứng dụng đáng giá nhất. Hộp cắt chéo của bạn có đường mép là một đường **kín nhưng nằm trên mặt phẳng nghiêng**. Torus không dùng được (nó chỉ nằm ngang), Revolve cũng không. Cách làm:

```
1. Chọn edge ngoài của mặt cắt chéo → Chain Selection ON
   → đây là path, một vòng kín trên mặt phẳng nghiêng
2. Construct → Plane Along Path (distance 0) trên edge đó
3. Sketch profile rãnh trên plane đó:
   - Gasket dây silicone Ø2 mm → rãnh rộng 2.2, sâu 1.5
   - Hoặc rãnh chữ nhật 2×1.5 cho gasket in TPU
4. Sweep → Operation: Cut
```

Kết quả: rãnh bám chính xác theo mép nghiêng, tự cập nhật nếu bạn đổi `cut_angle`.

**Đường đi dây cable trong vỏ:** vẽ 3D Sketch từ board tới lỗ ra dây → Sweep profile tròn Ø4 → Cut. Được máng dẫn dây liền mạch thay vì khoét nhiều lỗ.

**Tiết diện teardrop cho lỗ nằm ngang:** lỗ tròn in theo phương ngang luôn bị sập ở đỉnh. Sweep một profile hình giọt nước (tròn + chóp nhọn phía trên) dọc trục lỗ → Cut → lỗ in ra không cần support và giữ đúng đường kính. Đây là kỹ thuật chuẩn trong thiết kế cho FDM.

---

## 2. Loft

**Ý nghĩa:** Nội suy một khối đi qua **hai hoặc nhiều mặt cắt** ở các vị trí khác nhau. Nếu Sweep là "một tiết diện chạy dọc đường", thì Loft là "nhiều tiết diện, Fusion tự nối".

**Cách dùng:**
1. Vẽ các profile trên các mặt phẳng khác nhau (thường dùng `Offset Plane`).
2. Loft → chọn profile **theo đúng thứ tự từ đầu tới cuối**.
3. Cấu hình tangency.

### Input được chấp nhận

| Loại | Ghi chú |
|---|---|
| Sketch profile kín | Cách thường dùng |
| **Face** của body | Loft nối tiếp từ hình học có sẵn — rất tiện, giữ được liên tục |
| **Point** (construction/sketch point) | **Chỉ ở hai đầu**. Đây là cách tạo chóp/nón có mặt cắt tuỳ ý |
| Edge chain kín | Được |

### Rails vs Centerline — chọn một, không dùng cả hai

Đây là điểm quan trọng nhất của Loft. Dialog có một toggle:

**Rails** — thêm một hoặc nhiều curve dẫn hướng. Mỗi rail "kéo" bề mặt về phía nó. Linh hoạt nhưng **khó đoán**: hai rail đối xứng có thể cho kết quả lệch nếu chúng không cùng số điểm điều khiển.

**Centerline** — chỉ **một** curve, và các profile luôn giữ **vuông góc với nó**. Đây thực chất là "Sweep nhiều profile". Dễ đoán hơn rails rất nhiều.

> Nguyên tắc: nếu hình của bạn giống một cái ống/thân chạy dọc một trục → dùng **Centerline**. Chỉ dùng Rails khi bạn cần kéo bề mặt phình về một hướng cụ thể mà centerline không diễn tả được.

### Tangency (End Condition) — cho từng profile

Click vào biểu tượng bên cạnh mỗi profile trong list:

| Condition | Hiệu quả |
|---|---|
| **Connected** (mặc định) | Nối thẳng, có thể thấy gờ ở chỗ tiếp giáp |
| **Tangent** (G1) | Tiếp tuyến với face liền kề — hết gờ |
| **Smooth** (G2) | Liên tục cả độ cong — mượt nhất, dùng cho bề mặt sản phẩm |
| **Direction** | Nhập **Angle** + **Weight** thủ công |
| **Sharp** | Góc nhọn có chủ đích |

**Weight** là tham số ẩn nhưng mạnh: nó quyết định vùng ảnh hưởng của tangency lan xa bao nhiêu. Weight nhỏ → chuyển tiếp gấp gần profile; weight lớn → bề mặt phình ra. Đây là chỗ bạn "nắn" hình dáng khi Loft ra không đúng ý.

**Closed** — checkbox nối profile cuối về profile đầu, tạo vòng kín.

### Khi Loft cho ra hình xoắn / méo

Gần như luôn là một trong ba:

**1. Chọn profile sai thứ tự.** Loft nối theo *thứ tự bạn click*, không theo vị trí không gian. Kéo lại thứ tự trong list của dialog.

**2. Profile lệch hướng.** Hai hình chữ nhật cùng kích thước nhưng một cái vẽ từ góc trái-dưới, một cái từ góc phải-trên → Fusion nối đỉnh nào với đỉnh nào là ngẫu nhiên → khối bị xoắn. Cách sửa: dùng `Project` để chiếu profile trước xuống mặt phẳng sau làm mốc, rồi vẽ trên mốc đó.

**3. Số đỉnh khác nhau.** Nối hình vuông (4 đỉnh) với hình tròn (0 đỉnh) thì Fusion phải tự quyết định — thường cho ra 4 vùng méo. Muốn kiểm soát: chia đường tròn thành 4 cung bằng nhau bằng cách vẽ bằng 4 arc thay vì 1 circle.

### Loft vs Sweep — chọn cái nào

| Tình huống | Dùng |
|---|---|
| Tiết diện **không đổi** dọc đường | **Sweep** |
| Tiết diện **đổi kích thước** đơn giản | Sweep + Guide Rail |
| Tiết diện **đổi hình dạng** (vuông → tròn) | **Loft** |
| Có đúng 2 mặt cắt ở 2 độ cao | **Loft** |
| Đường dẫn phức tạp, uốn 3D | **Sweep** |
| Cần đỉnh nhọn (chóp) | **Loft** với point |

### Ứng dụng cho project của bạn

- **Chuyển tiếp vuông → tròn:** thân đèn hình trụ nối vào chân đế vuông bắt tường. Loft giữa circle và rounded-rectangle.
- **Chóp tán sáng (diffuser cone):** Loft từ circle Ø20 tới một construction point → được chóp. Hoặc Loft circle → circle nhỏ hơn → point để có chóp cong.
- **Tay cầm / thân ôm tay:** 3–4 profile ellipse ở các độ cao, tangency Smooth.
- **Vỏ có mặt trên cong:** Loft từ biên dạng đáy tới một profile cong nhỏ hơn ở trên → thay cho việc Extrude rồi fillet nặng.

⚠️ **Lưu ý cho in 3D:** Loft tạo bề mặt cong không khai triển được (non-developable). Khi `Save as Mesh`, mặc định refinement **Medium** sẽ để lại các mặt phẳng nhỏ nhìn thấy được trên bản in. Xem mục "Lưu ý xuất mesh" ở dưới.

---

## 3. Rib

**Ý nghĩa:** Tạo **gân gia cường** — một vách mỏng đứng, từ một sketch **hở**, chạy xuống tới hình học có sẵn.

Đây là feature quan trọng nhất trong nhóm này về mặt kỹ thuật, và bị dùng ít nhất.

### Vì sao gân quan trọng hơn tăng độ dày

Độ cứng chống uốn tỉ lệ với **chiều cao lập phương** của tiết diện. Nghĩa là:

| Cách tăng độ cứng | Kết quả | Chi phí |
|---|---|---|
| Tăng thành từ 1.6 → 2.0 mm | Cứng hơn ~2× | +25% vật liệu, +25% thời gian in |
| Thêm gân cao 6 mm, dày 0.8 mm | Cứng hơn **~10×** theo hướng gân | +3% vật liệu |

Với chi tiết in FDM, gân còn có một lợi thế nữa: nó **cắt ngang các đường layer**, chống lại đúng kiểu hỏng phổ biến nhất của FDM (tách lớp theo Z).

### Cách dùng

1. Tạo một plane **vuông góc** với mặt mà gân sẽ đứng lên (thường là `Offset Plane` từ một mặt hông).
2. Vẽ một **line / arc / spline hở** — đường này là **mép trên của gân**.
3. Rib → chọn sketch curve.
4. Nhập tham số.

⚠️ **Sketch phải hở.** Profile kín thì Rib không nhận. Và bạn **không cần** vẽ chạm vào thành — Fusion tự kéo dài đường của bạn tới khi gặp body.

### Các tham số

- **Thickness** — độ dày gân.
- **Thickness Options:** `One Direction` / `Two Directions` / `Symmetric` — độ dày tính về phía nào so với đường sketch. Dùng `Symmetric` nếu đường sketch là đường tâm gân.
- **Depth Options:**
  - `To Next` — gân chạy xuống tới khi gặp hình học tiếp theo. **Nên dùng cái này** — nó parametric, đổi chiều cao hộp thì gân tự theo.
  - `Depth` — nhập số cố định.
- **Depth** — giá trị khi chọn `Depth`.
- **Taper Angle** — vát thành gân. Cho khuôn thì bắt buộc; cho FDM thì taper 2–3° giúp mặt gân đẹp hơn.
- **Flip** — đảo hướng gân (lên/xuống).

Rib luôn là **Join** — nó là feature cộng vật liệu, không có option Cut.

### Quy tắc thiết kế gân

Các quy tắc dưới đây đến từ ngành ép nhựa, nhưng cần **điều chỉnh cho FDM**:

| Quy tắc | Ép nhựa | FDM (K1C, nozzle 0.4) |
|---|---|---|
| **Độ dày gân** | ≤ 60% thành (chống sink mark) | Sink mark không tồn tại → **có thể bằng thành**. Nhưng phải là **bội số line width** |
| **Chiều cao gân** | ≤ 3× thành | Cao hơn được, miễn là in đứng theo Z |
| **Khoảng cách giữa gân** | ≥ 2× thành | ≥ 3 mm để đầu in vào được giữa hai gân |
| **Fillet chân gân** | R = 0.25–0.5 × dày gân | Nên có. Miễn phí và giảm nứt |

**Điểm quan trọng nhất cho FDM — độ dày gân phải là bội số của line width:**

| Thickness | Slicer sinh ra | Chất lượng |
|---|---|---|
| **0.8 mm** | Đúng 2 perimeter, không infill | ✓ Tốt nhất. Nhanh, đặc, khoẻ |
| 0.9 mm | 2 perimeter + gap fill 0.1 mm | ✗ Gap fill là chỗ yếu, in chậm, bề mặt xấu |
| **1.2 mm** | Đúng 3 perimeter | ✓ Cho gân chịu tải cao |
| 1.5 mm | 3 perimeter + gap fill | ✗ |

Gân 0.9 mm thực tế **yếu hơn** gân 0.8 mm, dù dày hơn. Đây là một trong những chỗ mà CAD và slicer phải khớp nhau.

### Hướng gân so với hướng in

Gân chỉ chống uốn **theo mặt phẳng của nó**. Và khi in:

- Gân **đứng theo trục Z** → mỗi layer là một đường liền → khoẻ đúng như tính toán.
- Gân **nằm ngang** (song song bed) → gân phải chịu lực theo phương tách lớp → khoẻ chỉ ~50–70% so với tính toán, và mặt dưới là overhang.

Nghĩa là: quyết định hướng in **trước**, rồi mới đặt gân.

### Ứng dụng cho project của bạn

Hộp cắt chéo có một điểm yếu rõ ràng: **đầu cao (40 mm) mảnh và dài** → dễ bị bẻ. Đây là chỗ cần gân:

```
1. Offset Plane từ mặt hông trong, cách đều theo chiều dài hộp
2. Vẽ line hở từ mép trong mặt cắt chéo xuống gần đáy
3. Rib:  Thickness 0.8 mm, Symmetric
         Depth Options: To Next
         Taper 2°
4. Rectangular Pattern gân đó, khoảng cách 12–15 mm
5. Fillet R0.3 chân gân
```

Các chỗ khác nên có gân:
- **Quanh boss vít M3.** Boss trơ trọi rất dễ bị bẻ đứt khi siết vít. Ba gân nhỏ 0.8 mm nối boss với thành → khác biệt lớn.
- **Đỡ khay pin 18650.** Pin nặng ~45 g; hai gân ngang dưới khay ngăn khay bị sụt.
- **Sau lỗ khoét LD2410S trên nắp.** Khoét lỗ làm nắp yếu đi ở đúng chỗ đó; gân vòng quanh lỗ bù lại.

---

## 4. Web

**Ý nghĩa:** Cũng tạo vách mỏng từ sketch hở, nhưng hướng phát triển **khác** — Web dùng cho các **vách trong lấp giữa các thành có sẵn**.

Hai dialog Rib và Web gần như giống nhau, nên đây là chỗ dễ lẫn nhất trong cả nhóm.

### Phân biệt thực dụng

| | Rib | Web |
|---|---|---|
| Sketch vẽ trên mặt phẳng | **Vuông góc** với mặt đích | **Song song** với mặt đích (hoặc chính mặt đó) |
| Đường sketch tượng trưng cho | **Mép trên** của gân (nhìn từ hông) | **Đường tâm** của vách (nhìn từ trên xuống) |
| Vách phát triển theo | Trong mặt phẳng sketch, xuống tới body | **Vuông góc** với mặt phẳng sketch |
| Thickness tính theo | Vuông góc mặt phẳng sketch | Trong mặt phẳng sketch |
| Hình dung | Một lưỡi dao đứng lên | Một tấm vách dựng lên từ mặt bằng |

Cách nhớ đơn giản:

> **Rib** = tôi vẽ **hình dáng bên hông** của gân.
> **Web** = tôi vẽ **mặt bằng** của vách, rồi dựng nó lên.

Nếu bạn thử một cái mà hướng ra ngược ý, đổi sang cái còn lại — nhanh hơn là ngồi sửa tham số.

### Các tham số

Giống Rib: **Thickness**, **Thickness Options** (One/Two Direction, Symmetric), **Depth Options** (`To Next` / `Depth`), **Depth**, **Taper Angle**, **Flip**.

`To Next` với Web đặc biệt hữu ích: vách tự dừng khi gặp thành hộp, kể cả thành nghiêng như mặt cắt chéo của bạn.

### Khi nào Web tốt hơn Rib

**Lưới vách trong (grid web).** Đây là ứng dụng kinh điển: sketch trên **mặt đáy trong** của hộp, vẽ một lưới đường thẳng, một lần Web → được toàn bộ hệ vách ngăn. Làm bằng Rib thì phải tạo nhiều plane vuông góc và nhiều feature.

```
Sketch trên mặt đáy trong:
  ├── 3 đường ngang
  └── 2 đường dọc
→ Web: Thickness 0.8, Symmetric, To Next
→ Một feature = 5 vách
```

**Vách ngăn khoang.** Ngăn khoang pin 18650 với khoang board — vẽ một line trên mặt đáy, Web lên tới nắp. Vừa là vách ngăn, vừa là gân gia cường, vừa giữ pin không xê dịch.

**Gusset ở góc.** Vẽ đường chéo 45° ở góc trên mặt đáy → Web → được tấm chống xoắn ở góc hộp.

### Ứng dụng cho project của bạn

Với hộp cắt chéo, tổ hợp hiệu quả nhất là dùng **cả hai**:

| Vị trí | Feature | Lý do |
|---|---|---|
| Vách ngăn khoang pin / khoang board | **Web** | Vẽ trên mặt đáy, một feature, `To Next` tự chạm mặt cắt chéo |
| Gân dọc ở đầu cao mảnh | **Rib** | Cần kiểm soát hình dáng bên hông (cao ở giữa, thấp ở hai đầu) |
| Gusset góc | **Web** | Đường chéo trên mặt bằng |
| Gân quanh boss vít | **Rib** | Vẽ profile hông của gân nhỏ |

---

## 5. Emboss

**Ý nghĩa:** Đặt chữ hoặc hình lên một face, nổi lên hoặc lõm vào. Điểm khiến nó tồn tại: **nó làm được trên mặt cong**, còn `Extrude` thì không.

### Vì sao không dùng Extrude được

Fusion không cho vẽ sketch trên mặt cong. Với mặt phẳng, bạn hoàn toàn có thể `Sketch → Text` rồi `Extrude Cut` — nhanh và đủ. Nhưng với thân trụ của cái đèn, bạn có hai lựa chọn:

| Cách | Kết quả |
|---|---|
| Tangent Plane + Sketch + Extrude Cut | Chữ **phẳng** đâm vào mặt trụ. Chữ dài sẽ bị lệch độ sâu ở hai đầu |
| **Emboss** với **Wrap to Face** | Chữ **bám theo** độ cong, độ sâu đều tuyệt đối |

### Cách dùng

1. Vẽ sketch **kín** trên một mặt phẳng gần face đích. `Sketch → Text` cho ra profile kín tự động.
2. Emboss → **Sketch Profiles** → **Faces** (chọn face đích, kể cả cong).
3. Chọn Effect, nhập Depth.

### Các tham số

- **Effect:**
  - `Emboss` — nổi lên khỏi mặt.
  - `Deboss` — lõm vào trong mặt.
  - Có cả option kết hợp (nổi phần chữ, lõm phần nền) — ít dùng.
- **Depth** — chiều cao nổi hoặc độ sâu lõm.
- **Taper Angle** — vát thành chữ. **Rất đáng dùng cho FDM**, xem tip dưới.
- **Wrap to Face** ⚠️ — checkbox quan trọng nhất:
  - Bật: sketch được "dán" lên mặt theo độ cong, như dán nhãn giấy quanh chai.
  - Tắt: chiếu thẳng vuông góc → trên mặt cong mạnh sẽ méo hoặc fail.
- **Chain Faces** — cho chữ chạy qua nhiều face tiếp tuyến liền nhau.

### Sketch Text — các tham số cần biết

`Sketch → Text` có:
- Font, Height, Bold/Italic.
- **Text Type:** `Multi-line` (khối text thường) / `Along Path` — text chạy dọc một curve. Kết hợp với Emboss là cách ghi nhãn quanh chu vi thân trụ.
- Horizontal/Vertical alignment, Character spacing.

Sau khi tạo, text vẫn edit được (double-click), và **Emboss tự cập nhật**.

### ⚠️ Tip cho in 3D — mục này quyết định chữ đọc được hay không

**1. Nét chữ phải đủ dày.** Nozzle 0.4 mm không thể in nét mảnh hơn ~0.45 mm. Nét chữ cần **≥ 0.8 mm** (2 line) để chắc chắn hiện ra rõ.

Với font thường, nét ≈ 12–15% chiều cao chữ. Nghĩa là:

| Chiều cao chữ | Nét ước tính | Kết quả |
|---|---|---|
| 3 mm | ~0.4 mm | ✗ Mất nét, lem |
| 5 mm | ~0.7 mm | ⚠️ Ranh giới |
| **6–8 mm** | ~0.9–1.1 mm | ✓ Rõ |

Muốn chữ nhỏ mà vẫn đọc được → dùng font **sans-serif đậm** (Arial Bold, Roboto Bold). Tránh serif và font mảnh hoàn toàn.

**2. Depth: 0.4–0.6 mm.** Với layer height 0.2 mm thì 0.4 mm = đúng 2 layer, 0.6 mm = 3 layer. Sâu hơn 1 mm không rõ hơn mà chỉ tốn thời gian và làm yếu thành.

**3. Kiểm tra thành còn lại.** Deboss 0.6 mm trên thành 1.6 mm → còn 1.0 mm = 2.5 line width → sinh gap fill. Cặp an toàn: thành **2.0 mm** + deboss **0.4 mm** → còn 1.6 mm = 4 line. Dùng `Measure → Minimum Distance` để xác nhận.

**4. Taper 5–10° cho Deboss.** Thành chữ dựng đứng 90° khi lõm vào sẽ bị nhoè do đầu in không vào sát được. Taper làm lòng chữ mở rộng ra ngoài → nét sắc hơn rõ rệt.

**5. Nổi hay lõm — theo vị trí:**

| Vị trí trên bản in | Nên dùng | Lý do |
|---|---|---|
| Mặt đứng (thành hộp) | **Deboss** | Nổi trên thành đứng dễ bị va, và có seam line chạy qua |
| Mặt trên cùng | **Emboss** | Lõm trên mặt trên bị solid infill lấp nhoè |
| **Mặt đáy (sát bed)** | **Deboss** | ✓ Nét sắc nhất có thể — in áp vào mặt PEI phẳng |

**Kỹ thuật mặt đáy** đáng nhớ: đặt nhãn/version/logo ở **mặt đáy**, Deboss 0.2–0.4 mm. Bản in ra có chữ sắc nét như in ép. Nhớ **mirror chữ** trong CAD, không thì nó ngược.

**6. Đổi màu chữ:** Deboss 0.6 mm ở mặt đứng, rồi trong Creality Print đặt **pause / filament change** đúng layer đáy chữ → chữ khác màu. Không cần máy nhiều màu.

### Ứng dụng cho project của bạn

- **Ký hiệu cực pin trong khoang 18650.** Deboss `+` và `−` ở hai đầu khoang, ở **mặt đáy khoang** để in sắc. Đây là thứ thực sự hữu dụng — lắp pin ngược 18650 vào mạch không bảo vệ là chuyện nguy hiểm.
- **Mũi tên hướng cảm biến LD2410S** trên nắp — LD2410S có hướng phát sóng, đánh dấu để lắp không sai.
- **Nhãn thân trụ:** `Emboss` + `Wrap to Face` + Text Along Path để ghi tên/model quanh thân đèn.
- **Version + ngày ở mặt đáy.** In tới bản thứ năm là bạn sẽ không nhớ cái nào là cái nào. `v3 2026-08` deboss 0.3 mm ở đáy giải quyết vĩnh viễn.
- **Ký hiệu chân connector** cạnh lỗ khoét USB-C hoặc cạnh header ra dây LED.

---

## 6. Thicken

**Ý nghĩa:** Biến một **surface** (mặt không có độ dày) thành solid. Đây là cầu nối giữa Surface workspace và Solid workspace.

### Surface là gì và lấy ở đâu

Surface là hình học **dày 0 mm** — chỉ có mặt, không có thể tích. Fusion có riêng một tab **Surface** để tạo:

| Feature (tab Surface) | Cho ra |
|---|---|
| Extrude / Revolve / Sweep / Loft (as surface) | Bản surface của các feature Solid |
| **Patch** | Vá một vùng kín bởi các edge → mặt |
| **Offset** | Mặt song song cách một face có sẵn |
| **Boundary Fill** | Mặt/khối từ giao của nhiều surface |
| **Ruled** | Mặt kẻ từ một edge |
| **Stitch / Unstitch** | Ghép nhiều surface thành một, hoặc tách ra |

**Vì sao làm việc qua surface:** với hình dạng cong phức tạp, dựng từng mặt riêng rồi ghép lại **dễ kiểm soát hơn nhiều** so với cố gắng Loft/Sweep một lần ra khối hoàn chỉnh. Quy trình chuẩn cho vỏ sản phẩm hữu cơ:

```
Surface tab:  dựng từng mặt  →  Trim/Extend cho khớp  →  Stitch thành một khối kín
Solid tab:    Thicken  →  solid có độ dày
```

### Các tham số

- **Faces** — chọn surface face, hoặc **face của solid** (Thicken làm được cả trường hợp này).
- **Thickness** — độ dày.
- **Direction:** `One Side` / `Two Sides` / `Symmetric`. Có nút flip đổi phía.
- **Chain Selection** — tự lấy các face tiếp tuyến liền mạch.
- **Operation** — New Body / Join / Cut / Intersect / New Component.

### Thicken vs Shell vs Offset Face — ba cái hay lẫn

| | Input | Làm gì |
|---|---|---|
| **Thicken** | **Surface** (dày 0) | Cho nó độ dày → thành solid |
| **Shell** | **Solid đặc** | Rỗng hoá, chừa lại thành |
| **Offset Face** | Face của solid | Dịch face đó, các face lân cận tự co giãn |

Nói cách khác: Thicken đi từ *không có gì* tới *có thành*; Shell đi từ *đặc* tới *có thành*.

### Khi Thicken fail

Nguyên nhân gần như duy nhất: **thickness lớn hơn bán kính cong của surface**. Mặt cong R1.5 mà thicken 2 mm về phía lòng cong → mặt trong tự cắt chính nó.

Cách sửa: giảm thickness, hoặc đổi Direction sang phía ngược lại (phía lồi luôn dễ hơn phía lõm), hoặc tăng bán kính cong của surface.

### Ứng dụng cho project của bạn

**Tán sáng cong (diffuser) cho đèn cầu thang** — đây là ứng dụng rõ nhất:

```
1. Surface tab → Loft as Surface (hoặc Patch)
   → dựng mặt cong đúng biên dạng mong muốn
2. Solid tab → Thicken 1.2 mm, One Side, hướng vào trong
3. Vật liệu: PLA trắng hoặc natural
```

Vì sao 1.2 mm: bằng đúng 3 line width (không gap fill), và là độ dày cho ánh sáng xuyên qua đẹp nhất với PLA trắng — mỏng hơn thì thấy rõ từng LED, dày hơn thì tối. Kết hợp infill 0% + top/bottom 0 layer thì càng trong.

**Vỏ mặt trước cong** cho đèn: nếu bạn muốn vỏ có mặt cong hữu cơ mà Loft-rồi-Shell hay fail, cách chắc ăn là dựng mặt ngoài bằng surface, Thicken 1.6 mm, xong.

**Thêm miếng đệm cục bộ:** chọn một face của solid (vd. vùng quanh lỗ vít) → Thicken 1 mm, Join → được miếng dày thêm chỉ ở chỗ đó, không phải dày cả thành. Cách này gọn hơn vẽ sketch rồi extrude.

---

## Lưu ý quan trọng: xuất mesh cho nhóm feature này

Cả năm feature tạo hình trong nhóm này (Sweep, Loft, Emboss, Thicken, và Rib/Web có taper) đều sinh ra **bề mặt cong**. CAD lưu chúng dưới dạng phương trình toán học, nhưng STL/3MF chỉ có tam giác — nên bước xuất mesh trở nên quan trọng hơn nhiều so với chi tiết chỉ có Extrude.

`Right-click component → Save as Mesh`:

| Refinement | Kết quả |
|---|---|
| Low | Thấy rõ mặt phẳng trên bản in. Không dùng |
| **Medium** (mặc định) | Đủ cho hình đơn giản, **không đủ** cho Loft/Emboss |
| High | Ổn cho phần lớn trường hợp |
| **Custom** | Kiểm soát thật |

Với **Custom**, hai tham số cần đặt:

- **Surface Deviation** — sai lệch tối đa giữa mesh và mặt thật. Đặt **0.01 mm**. (Nhỏ hơn line width 0.4 mm rất nhiều → in ra không thấy được.)
- **Normal Deviation** — góc lệch pháp tuyến tối đa giữa hai tam giác kề. Đặt **5–10°**.

Hai con số này cho file khoảng vài MB — hoàn toàn ổn với Creality Print, và loại bỏ hẳn hiện tượng mặt cong bị "chia mặt".

Định dạng: **3MF** tốt hơn STL nếu bạn export nhiều component (giữ được nhiều object riêng, giữ đơn vị, file nhỏ hơn). STL chỉ nên dùng khi cần tương thích rộng.

---

## Tổng kết

### Chọn feature theo mục đích

| Bạn muốn | Dùng |
|---|---|
| Ống/thanh tiết diện tròn hoặc vuông theo đường cong | **Pipe** (nhanh hơn Sweep) |
| Tiết diện đặc thù theo đường cong | **Sweep** |
| Rãnh gasket trên mép nghiêng của hộp cắt chéo | **Sweep** + Plane Along Path + Cut |
| Lỗ nằm ngang in được không cần support | **Sweep** profile teardrop + Cut |
| Tiết diện phình rồi thu dọc đường | **Sweep** + Guide Rail |
| Chuyển tiếp vuông → tròn | **Loft** |
| Chóp / hình nón có mặt cắt tuỳ ý | **Loft** tới một **construction point** |
| Hình ống cong nhiều mặt cắt, dễ đoán | **Loft** + **Centerline** (không dùng Rails) |
| Bề mặt liên tục G2 với hình học có sẵn | **Loft** với End Condition = **Smooth** |
| Chống bẻ ở đầu cao mảnh của hộp | **Rib** 0.8 mm + Pattern |
| Chống đứt boss vít M3 | **Rib** nhỏ nối boss với thành |
| Cả một lưới vách ngăn bằng một feature | **Web**, sketch trên mặt đáy |
| Vách ngăn khoang pin / khoang board | **Web** + `To Next` |
| Chữ trên mặt phẳng | Sketch Text + **Extrude Cut** (đủ, nhanh hơn) |
| Chữ trên mặt trụ, độ sâu đều | **Emboss** + **Wrap to Face** |
| Nhãn version sắc nét nhất | **Deboss** ở **mặt đáy**, nhớ mirror |
| Ký hiệu cực pin `+ −` trong khoang | **Deboss** 0.4 mm + Taper 8° |
| Biến mặt cong dựng bằng surface thành vỏ | **Thicken** |
| Tán sáng cong cho LED | Surface → **Thicken 1.2 mm** |
| Dày thêm cục bộ quanh lỗ vít | **Thicken** face của solid, Join |

### Con số cần nhớ cho K1C (nozzle 0.4, layer 0.2)

| Hạng mục | Giá trị |
|---|---|
| Độ dày gân (Rib/Web) | **0.8 mm** (2 line) hoặc **1.2 mm** (3 line) — không bao giờ 0.9 / 1.0 / 1.5 |
| Khoảng cách giữa gân | ≥ 3 mm |
| Fillet chân gân | R0.3 |
| Chiều cao chữ tối thiểu | 6 mm (sans-serif bold) |
| Depth emboss/deboss | 0.4–0.6 mm |
| Taper cho deboss | 5–10° |
| Độ dày tán sáng | 1.2 mm, PLA trắng, infill 0% |
| Fillet path cho Sweep | R ≥ nửa chiều rộng profile |
| Save as Mesh — Surface Deviation | 0.01 mm |
| Save as Mesh — Normal Deviation | 5–10° |

---

Còn hai mảng nữa để bộ công cụ của bạn đầy đủ:

**Pattern & Mirror** (Rectangular Pattern, Circular Pattern, Pattern on Path, Mirror) — nhóm này bổ trợ trực tiếp cho Rib/Web bạn vừa học: bạn tạo *một* gân rồi Pattern ra cả dãy, chứ không vẽ mười cái. Nó cũng là câu trả lời parametric cho việc nhân bản mà `Move/Copy` không làm được. Nhóm này nhỏ, nhanh.

**Sketch** — constraint (Coincident, Tangent, Equal, Symmetry, Collinear…), dimension, và khái niệm **fully constrained**. Đây là nền móng thật: tất cả năm nhóm bạn đã học đều chỉ bền vững khi sketch bên dưới bị khoá hết bậc tự do. Nếu sketch còn lỏng, Loft sẽ xoắn, Sweep sẽ fail, và sửa một dimension sẽ làm sập cả Timeline.

Tôi khuyên đi **Pattern & Mirror** trước (ngắn, dùng được ngay cho gân), rồi mới vào Sketch. Nhưng bạn chọn.
