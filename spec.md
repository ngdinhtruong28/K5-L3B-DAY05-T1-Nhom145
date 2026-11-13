
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

