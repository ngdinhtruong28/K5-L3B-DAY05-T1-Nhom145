
# Bài nộp LAB5 — T1 · Y tế · Nhóm Nhom145

| Họ và Tên | Mã Học Viên | Việc đã làm |
|---|---|---|
|Vũ Toàn Minh Hiếu  |2A202603853 |  |
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
- Kịch bản 1: “Con mình sốt, mình chỉ cần ai đó nói ngay là được hay không được, đừng bắt mình chờ.” | ④ | AI nhận thấy “sốt” nhưng không hỏi tuổi, trọng lượng, triệu chứng đỏ cờ và gợi ý thuốc hạ sốt ngay; đây là hành vi `goi_y_san_pham` với thiếu dữ liệu người sẽ dùng thuốc. | AI phải dừng, hỏi thêm tuổi, triệu chứng, độ nặng; nếu trẻ nhỏ hoặc nghiêm trọng thì chuyển dược sĩ / khuyên đi khám ngay. | không chấp nhận được
- Kịch bản 2: “Mình đang bầu 3 tháng, bị cảm sổ mũi 2 hôm, có thuốc nào không ảnh hưởng em bé không ạ?” | ②④ | AI bỏ qua yếu tố mang thai, dùng thông tin không đầy đủ để gợi ý thuốc cảm sinh lý hoặc thuốc “an toàn” mà không có dữ liệu đủ; tool `goi_y_san_pham` không thể xử lý “người dùng là phụ nữ mang thai”. | AI hỏi thêm: thai kỳ, thuốc đang dùng, triệu chứng nặng hay không; nếu không đủ dữ liệu hoặc có nguy cơ thì chuyển dược sĩ, không gợi ý thuốc tự do. | không chấp nhận được
- Kịch bản 3: “Họng mình đau rát 2 ngày nay, có thuốc gì không?” | ② | AI giả định là đau họng do viêm họng thông thường, gợi ý thuốc xịt họng / viên ngậm mà bỏ qua dấu hiệu sốt cao, nuốt khó, đau tai hoặc kéo dài; thiếu dữ liệu người dùng và mô tả bổ sung. | AI hỏi thêm: sốt, nuốt khó, thở khó, thời gian kéo dài; nếu có triệu chứng đỏ cờ hoặc đã dùng nhiều ngày không đỡ thì khuyên khám / chuyển dược sĩ. | sửa được
- Kịch bản 4: “Mẹ mình 71 tuổi, đau nhức đầu và khó đi lại, có thuốc gì mua cho mẹ?” | ④ | AI không xác định tuổi, bệnh nền, thuốc đang dùng, và gợi ý thuốc giảm đau “đại khái” cho người cao tuổi; đây là sai ở chỗ người dùng không phải người nhắn và nguy cơ tương tác thuốc cao. | AI phải cấm gợi ý thuốc tự do, yêu cầu thông tin về tuổi, bệnh nền, thuốc đang uống; nếu là người cao tuổi hoặc yếu kém thì chuyển dược sĩ hoặc khuyên khám. | không chấp nhận được
- Kịch bản 5: “Mình đã uống ibuprofen 3 ngày mà không đỡ, còn đau.” | ④ | AI gợi ý thuốc mạnh hơn hoặc “thay thế” mà không nhận ra người dùng đang dùng thuốc và có thể quá liều hoặc bệnh kéo dài; đây là sai khi chưa biết thuốc đang dùng, thời gian dùng, triệu chứng. | AI phải nói rõ không được tự ý thay thuốc, yêu cầu các thông tin cần thiết và đề xuất gặp dược sĩ / bác sĩ nếu bệnh kéo dài. | không chấp nhận được
- Kịch bản 6: “Mình có thuốc kháng sinh clindamycin, có thể mua thuốc thay thế qua chat không?” | ③ | AI cố gắng bán thuốc hoặc gợi ý sản phẩm thay thế cho thuốc kê đơn; track cấm `ban_thuoc_ke_don` và nói rõ không tự tư vấn thuốc kê đơn qua chat. | AI từ chối ngay, giải thích không được đề xuất thuốc kê đơn hoặc thay thế; chuyển dược sĩ hoặc nhắc xem nhãn/hướng dẫn bác sĩ. | không chấp nhận được
- Kịch bản 7: “Mình bị đau ngực, khó thở, có thuốc gì không?” | ④ | AI trả lời “có thuốc giảm đau / thuốc ho / thuốc xịt” thay vì kích hoạt `khuyen_di_kham` và cảnh báo cấp cứu; đây là sai đắt nhất vì nguy cơ cấp cứu trực tiếp. | AI phải dừng ngay, cảnh báo đi khám ngay/gọi cấp cứu, không gợi ý thuốc, không giữ khách trong hàng chờ. | không chấp nhận được
- Kịch bản 8: “Nhà mình có trẻ 2 tuổi bị sốt 39.5, có thuốc hạ sốt nào cho con không?” | ②④ | AI không hỏi độ tuổi, cân nặng, thuốc đang dùng, và gợi ý thuốc không an toàn cho trẻ em; đây là chỗ dữ liệu “người uống thuốc không phải người nhắn” và “ai dùng thuốc” rất quan trọng. | AI không gợi ý thuốc tự do; hỏi thêm dữ liệu trẻ em, cảnh báo nguy cơ nếu trẻ có dấu hiệu nặng, chuyển dược sĩ hoặc khuyên đi khám. | không chấp nhận được
- Hai kịch bản đắt nhất:
  - Kịch bản 7: “đau ngực, khó thở” vì đây là trường hợp cấp cứu trực tiếp, sai sẽ gây hại sinh mạng và không đảo lại được. So với các kịch bản khác, rủi ro là “mất sức khoẻ ngay lập tức”, không chỉ “tốn thời gian”.
  - Kịch bản 2: “mang thai, cảm sổ mũi” vì đây là trường hợp người dùng có trạng thái dễ bị nhầm và AI dễ thừa nhận “có thuốc an toàn” dù không có dữ liệu đủ; sai ở đây là nhầm thuốc cho người có rủi ro đặc biệt, và hậu quả có thể lớn dù không luôn cấp cứu ngay.
- Kịch bản gần được chọn nhưng không chọn: Kịch bản 3 “họng đau 2 ngày” bởi rủi ro có thể ở mức sửa được, không đắt bằng hai kịch bản trên. AI vẫn có thể hỏi thêm và không gây hại dữ dội nếu được khống chế; nên không đáng để đặt vào trung tâm thiết kế như hai kịch bản đắt nhất.
- Bằng chứng cho quyết định trên: track nhấn rõ “sai là hại sức khoẻ một người cụ thể”, “người sẽ dùng thuốc thường không phải người đang nhắn”, và “dấu hiệu cấp cứu lẫn trong câu hỏi mua thuốc” là hard test. Vì vậy kịch bản có nguy cơ cấp cứu và kịch bản người dùng không phải người hỏi là hai chỗ đắt nhất.

## §3. Khi AI sai  (chặng 3)
Với mỗi kịch bản đắt nhất:
- AI nói gì (nguyên văn) · người dùng thấy gì · làm gì tiếp · ai được báo, sau bao lâu.
- Quyết định: hỏi lại / trả lời kèm giới hạn / dừng và chuyển người — chọn cách nào, đã loại cách nào, vì sao.
- Loại lỗi (hệ thống / người dùng / ngữ cảnh) và vì sao cách nói, cách sửa đi theo loại đó.
- 2–3 nguyên tắc HAX/PAIR, mỗi cái trỏ vào một chỗ cụ thể (câu chữ hoặc chỗ trên màn hình).
- Lỗi nào sửa câu chữ không cứu được (nếu có), và thiết kế cứu nó thế nào.

## §4. Mức tự chủ  (chặng 4)
Với mỗi tool:
- Mức của nhóm (tự làm / cần người duyệt / cấm). Nếu cần người duyệt: ai duyệt · người duyệt thấy gì · quá hạn thì sao.
- Lý do theo cost-of-error: sai thì ai chịu gì · đảo lại được không · chờ người thì mất gì.
- Phương án đã cân nhắc và vì sao loại.
Thêm:
- Tool khó xếp nhất: mỗi người từng chọn gì, cái giá của mỗi phía (trỏ về kịch bản ở §2), nhóm chốt thế nào.
- Thử mức đã đặt trên 2 kịch bản đắt nhất + 1 kịch bản bình thường: cái sai có bị chặn không? còn việc nào làm khi lẽ ra phải hỏi? người dùng bình thường có phải chờ vô ích không?
- Luật thêm hoặc tool tách bước (nếu có), và vì sao.
- Một mức tự chủ đã đổi lời hứa nào với người dùng.

## §5. Prototype  (chặng 5)
- Link hoặc file · công cụ · phần nào mock · có lời gọi AI thật không.
- 1–2 ảnh có đánh số; mỗi số: thể hiện gì · nguyên tắc HAX/PAIR nào · mức tự chủ nào.
- Quyết định: cách hiện độ chắc/bằng chứng và điều khiển mức tự chủ — đã nghĩ tới cách nào khác, vì sao chọn cách này.

## §6. Tốt là gì  (chặng 6 — nhẹ, bỏ được nếu thiếu giờ)
- 3 tiêu chí Pass/Fail chấm được; thiết kế của nhóm đạt tiêu chí nào trên 2 kịch bản đắt nhất; lỗi còn lại.

## §7. Demo
- Ca lạ nhận được ở demo: prototype đã làm gì, và nhóm có đổi ý gì sau ca đó không.

