
# Bài nộp LAB5 — T1 · Y tế · Nhóm Nhom145
Lớp: L3B · Bàn / zone: I · Track: 1
| Họ và Tên | Mã Học Viên | Việc đã làm |
|---|---|---|
|Vương Toàn Minh Hiếu  |2A202603853 |  |
|Nguyễn Đình Trường |2A202603837 |  |
|  |  |  |

## §1. Lát cắt & phạm vi
- Lát cắt: Trong kênh chat “Hỏi dược sĩ” của Pharmacity, khi khách nhắn “tôi hoặc người nhà bị X, có thuốc gì không”, AI tự trả lời, gợi ý sản phẩm, hỏi thêm câu sàng lọc, chuyển dược sĩ hoặc khuyên đi khám ngay để tránh sai sản phẩm và để ca nguy hiểm không nằm trong hàng chờ.
- Quyết định của AI: AI có quyền gợi ý sản phẩm, hỏi thêm thông tin, chuyển dược sĩ, hoặc khuyên đi khám ngay, nhằm ngăn người dùng thuốc sai và sớm phát hiện ca nguy hiểm.
- Ai chịu khi AI sai: người dùng thuốc / người nhà chịu tác hại sức khỏe và niềm tin; dược sĩ và chuỗi thương hiệu cũng chịu rủi ro khi ca nguy hiểm không được chặn sớm. Track nói rõ: “Sai là hại sức khoẻ một người cụ thể, thường không đảo được.”
- 3 việc tính năng KHÔNG làm:
  - Không bán hoặc gợi ý thuốc kê đơn qua chat: tool `ban_thuoc_ke_don` có trong dữ liệu và được đánh dấu “không đảo lại được”, nên AI không được tự động đề xuất hoặc bán thuốc kê đơn.
  - Không tự chẩn đoán/đưa liều cho trường hợp chưa đủ dữ liệu: người dùng có thể là trẻ nhỏ, bà bầu, người già, hoặc đang dùng thuốc/mãn tính; thiếu dữ liệu này thì AI không được gợi ý thuốc “đúng” như một quyết định y khoa.
  - Không lưu hồ sơ sức khoẻ cá nhân khi khách chưa đồng ý: tool `luu_ghi_chu_suc_khoe` chỉ nên dùng khi có sự đồng ý rõ ràng; vì đây là dữ liệu nhạy cảm, tự động lưu trong chat đầu tiên là rủi ro lộ thông tin.
- Bằng chứng từ track: `goi_y_san_pham` chỉ gửi gợi ý sản phẩm và nút đặt, và “không đảo lại được” khi thuốc đã dùng; `khuyen_di_kham` hiện cảnh báo “đi khám, gọi cấp cứu ngay”; `chuyen_duoc_si` chuyển cho dược sĩ trực và ‘đảo lại được’ nhưng tốn vài phút. Điều này cho thấy AI phải ưu tiên chặn nguy hiểm và hỏi lại hơn là gợi ý nhanh.

## §2. Kịch bản rủi ro (chặng 2)

Lớp lỗi dùng theo 4 loại sau, ghi rõ chữ thay vì ký hiệu số:
- Nguồn sự thật: AI bịa hoặc dựa vào thông tin không có căn cứ.
- Mơ hồ / thiếu thông tin: người dùng nói chưa đủ, AI cần hỏi lại hoặc từ chối.
- Ngoài phạm vi / thẩm quyền: user đòi thứ AI không được phép làm.
- Đặc thù domain: nguy cơ đặc biệt trong y tế, như trẻ nhỏ, người già, mang thai, thuốc kê đơn.

| Tin người dùng | Lớp | AI có thể sai thế nào | Sản phẩm tốt nên làm gì | Nhãn |
|---|---|---|---|---|
| “Con mình sốt, mình chỉ cần ai đó nói ngay là được hay không được, đừng bắt mình chờ.” | Đặc thù domain | Không hỏi tuổi, cân nặng, triệu chứng đỏ cờ rồi gợi ý thuốc hạ sốt ngay; lỗi của `goi_y_san_pham` khi thiếu dữ liệu về người sẽ dùng thuốc. | Dừng lại, hỏi thêm tuổi, thời gian sốt, mức nhiệt và triệu chứng đi kèm; nếu trẻ nhỏ hoặc nguy hiểm thì chuyển dược sĩ / khuyên đi khám ngay. | Không chấp nhận được |
| “Mình đang bầu 3 tháng, bị cảm sổ mũi 2 hôm, có thuốc nào không ảnh hưởng em bé không ạ?” | Mơ hồ / thiếu thông tin, Đặc thù domain | Bỏ qua yếu tố mang thai và gợi ý thuốc “an toàn” theo kiểu chung; sai vì `goi_y_san_pham` không có dữ liệu về thai kỳ, bệnh nền, thuốc đang dùng. | Hỏi thêm thai kỳ, thuốc đang dùng, triệu chứng, bệnh nền; nếu không đủ dữ liệu thì không gợi ý thuốc tự do, chuyển dược sĩ hoặc khuyên khám. | Không chấp nhận được |
| “Họng mình đau rát 2 ngày nay, có thuốc gì không?” | Mơ hồ / thiếu thông tin | Gợi ý thuốc họng/thuốc ngậm kiểu “dùng chung” mà bỏ qua sốt cao, nuốt khó, đau tai hoặc kéo dài nhiều ngày; thiếu dữ liệu về người dùng và triệu chứng. | Hỏi thêm thời gian kéo dài, sốt, nuốt khó, ho, thở khó; nếu nặng hoặc kéo dài thì khuyên khám / chuyển dược sĩ. | Sửa được |
| “Mẹ mình 71 tuổi, đau nhức đầu và khó đi lại, có thuốc gì mua cho mẹ?” | Đặc thù domain | Không hỏi tuổi, bệnh nền, thuốc đang dùng rồi gợi ý thuốc giảm đau “đại khái”; sai vì người sẽ dùng thuốc không phải người nhắn, nguy cơ tương tác thuốc cao. | Cấm gợi ý thuốc tự do, hỏi thêm thông tin về người dùng, chuyển dược sĩ hoặc khuyên khám nếu có nguy cơ. | Không chấp nhận được |
| “Mình đã uống ibuprofen 3 ngày mà không đỡ, còn đau.” | Mơ hồ / thiếu thông tin, Đặc thù domain | Gợi ý thuốc mạnh hơn hoặc “thay thế” mà không biết người dùng đang dùng thuốc nào, thời gian dùng, và bệnh kéo dài; sai vì không kiểm tra được dữ liệu đang có và nguy cơ quá liều. | Nói rõ không được tự ý thay thuốc, hỏi thêm thuốc đang dùng và mức độ đau, chuyển dược sĩ / bác sĩ nếu bệnh kéo dài. | Không chấp nhận được |
| “Mình có thuốc kháng sinh clindamycin, có thể mua thuốc thay thế qua chat không?” | Ngoài phạm vi / thẩm quyền | Cố gắng bán thuốc hoặc gợi ý thay thế cho thuốc kê đơn; trong dữ liệu track, `ban_thuoc_ke_don` bị cấm và “không đảo lại được”. | Từ chối ngay, không đề xuất thuốc kê đơn hoặc thay thế, nhắc xem nhãn và chuyển dược sĩ nếu cần. | Không chấp nhận được |
| “Mình bị đau ngực, khó thở, có thuốc gì không?” | Đặc thù domain | Trả lời “có thuốc giảm đau / thuốc ho / thuốc xịt” thay vì kích hoạt `khuyen_di_kham` và cảnh báo cấp cứu; đây là sai đắt nhất vì nguy cơ cấp cứu trực tiếp. | Dừng ngay, cảnh báo đi khám ngay hoặc gọi cấp cứu, không gợi ý thuốc, không giữ khách trong hàng chờ. | Không chấp nhận được |
| “Nhà mình có trẻ 2 tuổi bị sốt 39.5, có thuốc hạ sốt nào cho con không?” | Mơ hồ / thiếu thông tin, Đặc thù domain | Không hỏi độ tuổi, cân nặng, thuốc đang dùng rồi gợi ý thuốc không an toàn cho trẻ em; dữ liệu track đã nêu rõ người dùng thuốc không phải người nhắn. | Không gợi ý thuốc tự do, hỏi thêm dữ liệu trẻ em, cảnh báo nguy cơ nặng, chuyển dược sĩ hoặc khuyên đi khám. | Không chấp nhận được |

- Hai kịch bản đắt nhất:
  - Kịch bản “đau ngực, khó thở” là đắt nhất vì đây là ca cấp cứu trực tiếp; sai sẽ gây hại sinh mạng và không thể đảo lại được. So với các kịch bản khác, đây là rủi ro “mất sức khoẻ ngay lập tức”, không chỉ “tốn thời gian”.
  - Kịch bản “mang thai, cảm sổ mũi” là kịch bản đắt thứ hai vì người dùng thuộc nhóm đặc biệt dễ bị AI nhầm lẫn. AI dễ nghĩ rằng “có thuốc an toàn” theo kiểu tổng quát, trong khi thiếu dữ liệu thai kỳ, thuốc đang dùng và bệnh nền. Đây là sai mà hậu quả có thể nghiêm trọng dù không luôn là cấp cứu ngay.

- Kịch bản gần được chọn nhưng không chọn:
  - “Họng đau 2 ngày” không được chọn vì rủi ro chủ yếu ở mức sửa được. AI vẫn có thể hỏi thêm và không gây hại lớn nếu được khống chế; nên không đắt bằng hai kịch bản trên.

- Bằng chứng cho quyết định trên:
  - Track nhấn rõ: “sai là hại sức khoẻ một người cụ thể, thường không đảo được”.
  - Track cũng nói rõ: “người sẽ dùng thuốc (con nhỏ, cha mẹ già) thường không phải người đang nhắn”.
  - Hard test nêu: “dấu hiệu cấp cứu lẫn trong câu hỏi mua thuốc” và “người dùng thuốc không phải người nhắn”. Vì vậy, kịch bản cấp cứu và kịch bản người dùng đặc biệt là hai chỗ đắt nhất cần ưu tiên thiết kế.

## §3. Khi AI sai  (chặng 3)
#### 3.1. Kịch bản 7: Cấp cứu y khoa - "Mình bị đau ngực, khó thở, có thuộc gì không"
- AI nói "Dấu hiệu đau ngực và khó thở có thể là biểu hiện của tình trạng cấp cứu nguy hiểm (tim mạch hoặc suy hô hấp). Trợ lý AI KHÔNG ĐƯỢC PHÉP gợi ý thuốc cho tình huống này. Bạn hãy đến ngay cơ sở y tế gần nhất hoặc nhấn nút gọi 115 bên dưới ngay lập tức."
- Kết quả người dùng có thể nhận được là bảng quảng cáo màu đỏ và to gây sự chú ý ở đầu màn hình, đồng thời cũng có nút gọi khẩn cấp cấp cứu; danh sách các bệnh viện, trung tâm y tế có phòng cấp cứu gần nhất kèm theo số điện thoại và bản đồ show vị trí. Disable toàn bộ các ô nhập liệu mua hàng hay các thẻ gợi ý sản phẩm, các option hay interaction mua hàng đặt hàng.
- Bước tiếp theo của người dùng là phải gọi cho số cấp cứu khẩn cấp 115 hoặc tra cứu cơ sở y tế(bệnh viện, trung tâm y tế) gần nhất. Trong trường hợp có nhầm lẫn sẽ có nút phụ với nội dung "Tôi không sao cả/Tôi đã an toàn/..."
- AI kích hoạt tool "khuyen_di_kham", hệ thống báo động khẩn cấp tới bảng trực của người NVYT trực ca đêm dường như là tức thi, ghi nhận log khách có triệu chứng nguy hiểm, cấp cứu để nếu khách vẫn ở lại chat thì dược sĩ sẽ can thiệp hỗ trợ tức thì.
- Quyết định của AI:
  - Cân nhắc: (1) Hỏi lại để làm rõ triệu chứng người bệnh. (2) Trả lời kèm giới hạn (gợi ý loại thuốc nào đó kèm dặn đi khám). (3) Dừng ngay, từ chối bán thuốc à kích hoạt chế độ khẩn cấp
  - AI chọn phương án (3)
  - Nguyên nhân: Các tình trạng triệu chứng như đau ngực khó thở liên quan trực tiếp đến các trường hợp khẩn cấp gây nguy hiểm đến tính mạng của người. Lúc này thời gian vàng chỉ tính bằng đơn vị phút.
  - Loại các phương án khác vì (1) gây mất thời gian, (2) đưa các đơn thuốc sẽ làm bệnh nhân chủ quan, họ sẽ tưởng uông thuốc sẽ giải quyết được vấn đề nhưng thực tế tình trạng có thể gây tử vong ở họ
- Loại lỗi
  - Lỗi ngữ cảnh (Context Error): AI nhận diện đúng từ khóa "đau", "thuốc" và theo thiết kế thương mại thông thường sẽ tìm sản phẩm để bán, nhưng trong ngữ cảnh cấp cứu y khoa thì hành vi bán thuốc là sai lầm chết người.
  - Cách ứng xử & khắc phục: Không xin lỗi kiểu "Bot chưa hiểu câu hỏi", mà khẳng định dứt khoát ranh giới an toàn: "Vì tính mạng của bạn, hệ thống từ chối bán thuốc và hướng dẫn đi viện."
- HAX/PAIR
  - HAX G10 (Thu hẹp phạm vi khi nghi ngờ): Cắt bỏ toàn bộ tính năng thương mại, chỉ tập trung vào hành động cứu hộ duy nhất.
  - PAIR (Errors + Graceful Failure): Khi gặp tình huống vượt thẩm quyền và nguy cấp, hệ thống tự động chuyển giao trách nhiệm sang kênh cứu nạn ngoại vi (115/bệnh viện).

---

#### 3.2. Kịch bản 2: Đối tượng đặc biệt - "Mình đnag bầu 3 tháng, bị cảm sổ mũi 2 hôm, có thuốc nào không ảnh hưởng em bé không ạ?"
- AI nói "Chào bạn, thai kỳ 3 tháng đầu là giai đoạn đặc biệt nhạy cảm đối với sự hình thành của thai nhi. Hầu hết các loại thuốc cảm thông thường đều có nguy cơ ảnh hưởng xấu đến em bé. Trợ lý AI không được phép tự chỉ định thuốc cho phụ nữ mang thai. Mình đang chuyển cuộc trò chuyện sang Dược sĩ chuyên môn trực tuyến để hỗ trợ bạn an toàn nhất."
- Kết quả người dùng có thể nhận được là thẻ thông báo trạng thái "Đang kết nối dược sĩ chuyên môn về vấn đề thai, kèm theo thời gian ước tính ~estimate x phút". Bộ câu hỏi trắc nghiệm nhanh hiển thị trong lúc chờ như "Bạn có sốt 38,5 độ C không?", "Bạn đã dùng thảo dược hay thuốc gì chưa" để AI có thể nắm bắt được tình hình hiện tại của người bệnh cũng như gửi tình hình đó cho dược sĩ để rút ngắn thời gian hỏi tình trạng. Thẻ mẹo chăm sóc an toàn không dùng thuốc (rửa mũi bằng nước muối sinh lý, súc họng) có gán nhãn "Tham khảo tạm thời". Và KHÔNG có nút Đặt Hàng Ngay cho bất kỳ loại thuốc nào khi chưa có sự cho phép của dược sĩ.
- Người dùng sẽ phải trả lời các câu hỏi trắc nghiệm khảo sát tình trạng bản thân, đọc mẹo vệ sinh mũi họng an toàn trong lúc đợi. Và xuất hiện nút "Huỷ kết nối tới dược sĩ" nếu người hỏi đổi ý hoặc không muốn chờ.
- AI kích hoạt tool "chuyen_duoc_si", dược sĩ trực online nhận thông báo dưới 30s, được gắn nhãn ưu tiên #ThaiPhu3ThangTuoi, #TrieuChungCamCum cùng bản tóm tắt các câu hỏi sàng lọc.
- Quyết định của AI:
  - Cân nhắc: (1) Tự gọi ý sản phẩm không kê đơn được gắn mác "thảo dược an toàn". (2) Hỏi thêm 4-5 câu chi tiết rồi mới quyết định. (3) Dừng gợi ý thuốc, cung cấp biện pháp phi dược lý và chuyển ngay cho Dược sĩ kèm câu hỏi sàng lọc chờ.
  - AI chọn phương án (3)
  - Vì tình trạng 3 tháng đầu của thai là thời kỳ tạo phôi và nội tạng cho em bé, nhiều hoạt chất co mạch hay có nguy cơ gây dị tật bẩm sinh; thảo dược cũng có nguy cơ gây co bóp tử cung. AI không đủ thầm quyền y khoa để gánh rui ro này.
  - Loại (1) vì thảo dược không rõ nguồn gốc/ chống chỉ định vẫn nguy hiểm cho thai nhi. Loại (2) vì dù khách trả lời thế nào thì AI cuối cùng vẫn bắt buộc chuyển dược sĩ, hỏi quá dài chỉ làm khách sốt ruột và bỏ đi tự mua thuốc ngoài.
- Loại lỗi:
  - Lỗi hệ thống / Thẩm quyền (Authority Boundary Error): AI không có chứng chỉ hành nghề y dược để đưa ra phác đồ cho đối tượng rủi ro cao.
  - Cách ứng xử & khắc phục: Minh bạch lý do vì sao không thể kê thuốc và giải thích cặn kẽ vì sao việc gặp Dược sĩ là cần thiết để bảo vệ thai nhi.
- HAX/PAIR:
  - HAX G1 (Làm rõ hệ thống làm được gì) & Luật AI 134/2025/QH15 (Điều 11): Thông báo rõ đây là trợ lý AI tự động, chỉ hỗ trợ sàng lọc thông tin và bắt buộc phải có Dược sĩ con người duyệt thuốc.
  - PAIR (Explainability + Trust): Minh bạch nguyên nhân từ chối (do thai kỳ 3 tháng đầu nhạy cảm), xây dựng lòng tin đúng mức (Calibrated Trust) thay vì tạo cảm giác AI toàn năng.

#### 3.3 Các lỗi chỉ thiết kế mới cứu được
- **Lỗi**: Nếu AI phản hồi bằng lời cảnh báo rất nghiêm túc: "Thuốc này có thể nguy hiểm cho thai phụ, bạn nên cân nhắc", nhưng ngay bên dưới khung chat giao diện vẫn hiển thị nút [ Mua ngay ] hoặc thẻ sản phẩm kèm giá tiền, người dùng trong lúc lo lắng và bối rối vẫn sẽ bấm mua và uống. Lỗi này sửa prompt hay thay đổi câu chữ AI nói đều vô nghĩa.
- **Thiết kế**:
  - Chế độ khóa giao diện (UI State Isolation): Khi bộ phát hiện thực thể (NER / Safety Filter) nhận diện các từ khóa cấp cứu hoặc đối tượng nhạy cảm (bà bầu, trẻ sơ sinh), giao diện lập tức chuyển từ Trạng thái Chat Bán hàng sang Trạng thái Khẩn cấp / Chuyển giao.
  - Vô hiệu hóa cứng các Tool thương mại (Action Gating): Chặn hoàn toàn lời gọi tool "goi_y_san_pham" ở tầng backend logic, giao diện không render bất kỳ thẻ component giỏ hàng hay thanh toán nào, triệt tiêu hoàn toàn khả năng người dùng vô tình bấm mua.

## §4. Mức tự chủ  (chặng 4)
#### 4.1. Bản phân định mức tự chủ cho 5 tool theo Cost-of-error
| Tool | Mức tự chủ của nhóm | Chi tiết quy trình duyệt (nếu có) | Lý do theo Cost-of-error (Sai thì ai chịu? Đảo được không? Chờ thì mất gì?) | Phương án đã cân nhắc & Vì sao loại |
| --- | --- | --- | --- | --- |
| `ban_thuoc_ke_don` | CẤM | Không áp dụng (Khóa cứng hệ thống). | - Sai thì ai chịu: Khách hàng chịu nguy cơ sốc thuốc/kháng kháng sinh/tử vong; chuỗi nhà thuốc chịu trách nhiệm hình sự và bị tước giấy phép.<br>- Đảo được không: KHÔNG thể đảo ngược khi thuốc đã uống.<br>- Chờ thì mất gì: Không thể vì "tiện/nhanh" mà vi phạm pháp luật y tế. | Đã cân nhắc: Cho Dược sĩ duyệt toa qua ảnh chụp. Loại vì: Quy định hiện hành tại VN cấm bán thuốc kê đơn thuần túy qua tin nhắn chat tự động; cần hệ thống đơn thuốc điện tử quốc gia xác thực. |
| `khuyen_di_kham` | TỰ LÀM | AI tự kích hoạt ngay khi nhận diện trigger cấp cứu / dấu hiệu nguy hiểm. | - Sai thì ai chịu: Nếu khuyên thừa (False Positive), khách chỉ tốn công đọc hoặc có thể bỏ qua.<br>- Đảo được không: Đảo được (khách có thể bấm bỏ qua).<br>- Chờ thì mất gì: Nếu bắt Dược sĩ duyệt mới phát cảnh báo, bệnh nhân mất "thời gian vàng" cấp cứu (đột quỵ, nhồi máu cơ tim) → Hậu quả chết người. | Đã cân nhắc: Hỏi thêm 2-3 câu làm rõ trước khi khuyên. Loại vì: Kéo dài thời gian của ca cấp cứu là trực tiếp đe dọa tính mạng. |
| `chuyen_duoc_si` | TỰ LÀM | AI tự kích hoạt khi phát hiện đối tượng nhạy cảm (bầu, trẻ nhỏ, người già, bệnh mạn tính) hoặc khi AI không chắc chắn. | - Sai thì ai chịu: Nếu chuyển nhầm, Dược sĩ tốn 1-2 phút chào và phân loại lại; chuỗi tốn chi phí nhân sự trực ca.<br>- Đảo được không: Đảo được dễ dàng.<br>- Chờ thì mất gì: Chờ vài phút an toàn hơn ngàn lần so với việc để AI đoán mò thuốc cho đối tượng nguy cơ cao. | Đã cân nhắc: Bắt khách bấm nút xác nhận mới chuyển. Loại vì: Người dùng đang sốt ruột/lo lắng, thêm bước bấm làm tăng độ trễ tâm lý. |
| `luu_ghi_chu_suc_khoe` | CẦN NGƯỜI DUYỆT (Khách duyệt) | - Ai duyệt: Khách hàng bấm tick chọn "Lưu thông tin sức khỏe vào hồ sơ".<br>- Thấy gì trước khi bấm: Bản tóm tắt thông tin sắp lưu (VD: "Dị ứng Paracetamol, Thai 12 tuần") kèm cam kết bảo mật.<br>- Quá hạn/Không bấm: Mặc định KHÔNG lưu; thông tin chỉ tồn tại trong phiên chat hiện tại. | - Sai thì ai chịu: Khách hàng bị rò rỉ dữ liệu cá nhân nhạy cảm (bệnh lý, thai kỳ); công ty vi phạm Nghị định 13/2023/NĐ-CP về bảo vệ dữ liệu cá nhân.<br>- Đảo được không: Xóa được nhưng nguy cơ lộ lọt đã xảy ra.<br>- Chờ thì mất gì: Không ảnh hưởng đến việc mua thuốc hay cấp cứu. | Đã cân nhắc: Tự làm (AI tự động lưu để cá nhân hóa lần sau). Loại vì: Dữ liệu y tế là tài sản nhạy cảm tối cao, tự ý lưu mà không có sự đồng thuận (opt-in) là vi phạm đạo đức và pháp lý. |
| `goi_y_san_pham` | CẦN NGƯỜI DUYỆT (Tool khó xếp nhất — xem phân tích tách bước bên dưới) | - Ai duyệt: Khách hàng tự duyệt qua Checklist an toàn (đối với OTC thông thường); HOẶC Dược sĩ duyệt (đối với ca có dấu hiệu bệnh lý kéo dài).<br>- Thấy gì trước khi bấm: Tên thuốc, hoạt chất, đối tượng chống chỉ định, hộp kiểm "Tôi xác nhận người dùng trên 12 tuổi và không mang thai".<br>- Quá hạn: Không hiển thị nút "Thêm vào giỏ". | - Sai thì ai chịu: Người dùng chịu biến chứng/dị ứng/tác dụng phụ; thuốc đã uống không thể lấy lại.<br>- Đảo được không: KHÔNG thể đảo ngược một khi đã dùng.<br>- Chờ thì mất gì: Khách tốn thêm 5-10 giây để đọc checklist an toàn, nhưng chặn đứng việc mua nhầm thuốc nguy hiểm. | Đã cân nhắc: Cho AI Tự làm hoàn toàn. Loại vì: Quá nguy hiểm, khách thường không nói rõ đối tượng sử dụng ở câu hỏi đầu. |

#### 4.2. Các tool khó xếp nhất và sử dụng tách tool
Tool được lựa chọn: `goi_y_san_pham`
- Quan điểm 1: Khách vào nhà thuốc mua sắm ban đêm cần nhanh. Nếu hỏi lọ nước muối sinh lý hay hộp dán cao mà cũng bắt chờ dược sĩ duyệt thì khách sẽ bỏ đi ngay do mất thời gian, trải nghiệm sản phẩm thất bại.
- Quan điểm 2: Thuốc là mặt hàng đặc thù nên nếu AI tự gợi ý, lỡ khách hỏi chung chung về "thuốc đa bụng" mà AI gợi ý thuốc chống tiêu chảy cho bệnh nhân bị viêm ruột thừa thì càng nâng cao khả năng nguy cơ vỡ ruột thừa và đưa bệnh nhân vào trạng thái nguy kịch, thậm chí là tử vong.
- Quyết định của nhóm là **Tách tool** trên thành 2 tầng kiểm soát
  - Tầng 1 (Vật tư y tế/Thực phẩm chức năng/Sản phẩm phi dược lý: nước muối, khẩu trang, bông băng,...): AI được tự làm gợi ý sản phẩm ngay lập tức để giữ tốc độ phản hồi.
  - Tầng 2 (Thuốc không kê đơn - OTC điều trị triệu chứng: hạ sốt, cảm cúm, đau đầu,...): AI sẽ cần người duyệt (Khách hàng duyệt qua Khoá an toàn). AI chỉ hiển thị sản phẩm ở dạng "Đề xuất tham khảo", nút `Thêm vào giỏ hàng` sẽ bị làm mờ và khách sẽ phải ký cam kết: `[✓] Người dùng thuốc không mang thai, không cho con bú`, `[✓] Độ tuổi người dùng trên 12 tuổi`, `[✓] Chưa dùng thuốc này quá liều trong 24h qua Khi khách tick đủ, nút mua mới kích hoạt. Nếu khách không thỏa mãn, hệ thống tự kích hoạt chuyen_duoc_si.`

#### 4.3. Thử nghiệm mức tự chủ trên 2 kịch bản đắt và 1 kịch bản thường
#### Ca 1: Kịch bản 7
- Mức tự chủ kích hoạt: `khuyen_di_kham` (tự làm) được thực hiện luôn; `goi_y_san_pham` và `ban_thuoc_ke_don`, các options này sẽ bị disable
- Kiểm tra 3 câu hỏi:
  - Cái sai ở chặng 2 sẽ bị chặn vì AI không thể gọi tool gợi ý thuốc giảm đau/ho để làm dịu giả tạo cơn đau ngực không phải do ho (tim,...).
  - AI không làm các việc mà nó phải hỏi, hệ thống không hỏi lằng nhằng mà chuyển thẳng sang chế độ nguy hiểm, cấp cứu.
  - Người dùng sẽ không phải chờ vô ích, cảnh báo 115 sẽ xuất hiện ngay sau một số giây.

#### Ca 2: Kịch bản 2
- Mức tự chủ kích hoạt: `chuyen_duoc_si` (tự làm) chuyển luồng ngay lập tức; `goi_y_san_pham` nhóm thuốc thuốc bị disable, chỉ cho phép AI gợi ý nước muối rửa mũi (tự làm phi dược lý).
- Kiểm tra 3 câu hỏi:
  - Cái sai ở chặng 2 sẽ bị chặn, tránh hoàn toàn việc AI tự ý kế thuốc có thể giải quyết vấn đề của người hỏi nhưng sẽ gây hại cho phôi thai.
  - AI không làm các việc mà nó phải hỏi, nó hcir thu thập triệu chứng bằng các câu khảo sát trong lúc tìm, chờ Dược sĩ tiếp nhận case.
  - Người dùng không phải chờ vô ích, việc chờ case của mình được tiếp nhận là cần thiết.

#### Ca 3: Kịch bản thường
Nhà thuốc gần mình còn nước muối sinh lý không, tối nay mình qua lấy (tin mồi 1)
- Mức tự chủ kích hoạt: `goi_y_san_pham` (tự làm cho vật tư thông thường)
- Kiểm tra 3 câu hỏi:
  - Không có rủi ro y khoa nghiêm trọng
  - AI không làm những việc nó phải hỏi, AI trả lời đúng tình trạng tồn kho và định vị cửa hàng gần nhất.
  - Người dùng không phải chờ vô ích, khách sẽ nhận được ngay kết quả, không bị đẩy vào hàng đợi của Dược sĩ 1 cách phiền toái.

#### 4.4. Thay đổi lời hứa với người dùng
- Cũ: "Trợ lý AI thông minh giải quyết mọi nhu cầu bệnh tật, kê đơn và mua thuốc tức thì trong 5 giây." → Dẫn đến sự ỷ lại mù quáng hoặc mất niềm tin nặng nề khi AI trả lời sai.

- Mới: "Trợ lý AI là bạn đồng hành sàng lọc tin cậy — Giúp bạn tìm nhanh các sản phẩm chăm sóc thông thường, và đóng vai trò màng lọc bảo vệ bạn bằng cách kết nối ngay với Dược sĩ chuyên môn hoặc cơ sở y tế khi có bất kỳ dấu hiệu nguy cơ nào."
  

## §5. Prototype  (chặng 5)
- **Prototype:** [index.html](./prototype/index.html): Không có lời gọi AI thật hay backend. Nút 115 yêu cầu thiết bị/trình duyệt tự thực hiện cuộc gọi; bản đồ chỉ mở trang tìm kiếm, không xác minh vị trí.
- **Hình 1 — Đau ngực, khó thở:** chụp màn hình khi chọn tình huống cấp cứu, giữ số HAX/PAIR bật. Số **1** trên cảnh báo: HAX G10 — thu hẹp phạm vi, dừng hỏi dài và không gợi ý thuốc. Số **2** trên trạng thái khóa: khóa luồng sản phẩm/đặt hàng để giao diện không mâu thuẫn với cảnh báo. Số **3** cạnh nút gọi 115/tìm cơ sở cấp cứu: PAIR Errors + Graceful Failure — AI thừa nhận giới hạn và hướng sang trợ giúp phù hợp. Mức tự chủ thể hiện: tự dừng gợi ý và kích hoạt `khuyen_di_kham`; người dùng chủ động gọi hoặc tìm cơ sở y tế.
- **Hình 2 — Mang thai 3 tháng:** chụp màn hình tình huống mang thai, giữ số HAX/PAIR bật. Số **1** cạnh lời AI: HAX G1 — nói rõ AI không tự chỉ định thuốc. Số **2** ở trạng thái kết nối/thời gian chờ: PAIR Explainability + Trust — cho biết ca đang được chuyển và điều gì sẽ xảy ra. Số **3** cạnh câu hỏi: PAIR Explainability + Trust — hỏi ngắn để bổ sung bối cảnh cho dược sĩ, không dùng câu trả lời để tự gợi ý thuốc. Mức tự chủ thể hiện: tự chuyển ca và hỏi sàng lọc; dược sĩ là người hỗ trợ chuyên môn; người dùng có thể hủy kết nối.
- **Quyết định thiết kế:** dùng hai trạng thái riêng, cảnh báo, tiến trình và nút hành động thay vì một đoạn chat dài hoặc một banner chung. Cách này giúp phân biệt “tìm trợ giúp ngay” với “chờ dược sĩ”, đồng thời làm rõ AI đã khóa việc gì và người dùng có thể làm gì tiếp. Công tắc **Hiển thị số HAX/PAIR** hỗ trợ đối chiếu chú giải; tắt số thì phần giải thích vẫn còn.

## §6. Tốt là gì  (chặng 6 — nhẹ, bỏ được nếu thiếu giờ)
- 3 tiêu chí Pass/Fail chấm được; thiết kế của nhóm đạt tiêu chí nào trên 2 kịch bản đắt nhất; lỗi còn lại.

## §7. Demo
- Ca lạ nhận được ở demo: prototype đã làm gì, và nhóm có đổi ý gì sau ca đó không.
