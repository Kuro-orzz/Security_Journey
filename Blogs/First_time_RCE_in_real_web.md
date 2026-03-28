# My First Journey to a Real-World RCE: From a Simple Bug to Full System Compromise

> ### Note 1: Xin phép ko public tên web =)))

> ### Note 2: Chắc lúc bài viết này được public thì web cũng fix vuln rồi:))), thật sự thì mình cũng ko đủ can đảm để đăng lúc chưa fix đâu.

> ### Note 3: Lần đầu test Windows Web Server, khá bỡ ngỡ, ko quen cmd của win nên thuần prompt:))

## Table of Contents
1. [Day 1: Reconnaissance & Scanning](#day-1)
2. [Day 2: Gaining Access & Local Privilege Escalation (LPE)](#day-2)
3. [Day 3: Exploring](#day-3)
4. [Day 4: The Final Exploit & Cracking](#day-4)
5. [Key Takeaways](#bài-học-rút-ra)


## Day 1:

### Phase 1: Reconnaissance web UI
- Đó là vào một buổi sáng sớm, cụ thể là 1h sáng 😗, có 1 con người vẫn đang cố chấp dùng Burp Suite để pentest web. Đó là chấp niệm muốn có "lần đầu làm chuyện ấy" với cái web ấy =)))) 
- Mục tiêu là 1 website mà nếu chỉ nhìn qua sẽ ko thấy có gì đặc biệt, rất ít tính năng để có thể pentest ra được bug. Tuy nhiên, có 1 tính năng đã lọt được vào mắt xanh của mình, đó là upload avatar feature. (hoặc đơn giản là vào đúng giai đoạn mình đang học thêm về cái này:)))
- Giai đoạn đầu thì mình có test thử 1 vài file extension khác nhau, nhưng không cái nào thành công
- Sau đó mình có thử mở dev tool để check xem có gì lạ bị leak lên local storage, response hay source code ko. Thì đúng như kì vọng, quả source code check file extension nằm ở FE bị leak lên full HD luôn:)))
- Dựa vào đó, mình thấy web in ra là cho phép file ext `jpg, bmp, png`, nhưng trong code thì còn cho phép cả file `gif` nữa.

### Phase 2: Scanning and Enumeration
- Ban đầu thì mình chuyên tâm vào gif hơn vì nghĩ có nhiều khả năng injection hơn, nhưng điểm qua vài trick lỏd mà vẫn không được nên mình quyết định gác lại chuyển 1 số CVE, CWE-34 hay những CVE liên quan đến nó, thậm chí là dò hỏi Gemini liên tục.
- Thấm thoát đã sang đến 2 rưỡi sáng, dù đã thử qua vài tool và vài trick lỏd nhưng vẫn ko cái nào thực sự có hiệu quả lên web. Thật sự lúc đấy mình cũng nản lắm rồi, tính lên giường đi ngủ luôn để sáng dậy đi học cơ, nhưng nhờ thế lực thần bí nào đó mà mình vẫn ngồi test tiếp =)))
- Đến tầm đâu đó tầm hơn 3h sáng, mình có tìm được 1 blog nói về Client-side Validation Bypass, thế là mình bắt tay vào thử. Test xong, mình rất muốn hét lên "Euréka"😂, nhưng nhìn lại phòng thấy các cháu ngủ ngon qua nên thôi 😚.
- Thật sự thì mình ko ngờ tới nước đi này, web thì mấy chục ngàn người dùng mà lại hớ hênh đến độ này. 🤨
- Lúc đó mình cũng khá mệt nên cũng ko nghĩ ngợi gì nữa mà đi ngủ luôn.


### Phase 3: Reconnaissance server (After explore RCE vulnerability)
- Lúc này, mình đã lên lớp ngồi học, nhưng cố tình xuống bàn cuối ngồi một mình để tiện test web.
- Sau khi có được RCE trong tay, mình thử thu thập những thông tin cơ bản của server, đầu tiên thì những phát hiện của mình là:
    - Đây là 1 con Windows web server 2016, OS version là win10x64.
    - Role hiện tại khi mình RCE mặc định là Low-privileged Service Account.
    - Check `tasklist` xong hỏi gemini thì nó cho mình biết 1 số process đang dùng để chạy web.
    - Có sử dụng cả WAN và LAN
- Đây là lần đầu mình RCE qua shell của Windows server nên khá bỡ ngỡ.
- Trước giờ mình toàn host web server Linux nên thực sự mình không quen lắm và phải hỏi gemini khá nhiều thứ, từ những lệnh cơ bản như `type` để in data ra. Mình cũng nhờ gemini generate code .asp có chút giao diện để dễ thao tác với RCE hơn thay vì chỉ chỉnh trên url.
- Sau đó mình dạo một vòng quanh server, quan sát vị trí hiện tại bằng `cd` (cuz of this make i misleading 🥲). Sau đó đi qua các thư mục trong ổ C, check cả ngày thay đổi cũng như các tệp được sửa gần đây, sau đó mình đi đến folder `C:\Users`, ở đây thì mình bị chặn lại, không vào được folder `Administrator` do permission quá thấp, do đó ko đủ khả năng check trong đó có gì, lúc đó dù đã nhờ gemini thử mọi cách vẫn ko cách nào qua được.
- Đến chiều hôm đó, vì cay cú quá nên mình đã tìm đến 1 thứ tà đạo, đó là `Local Privilege Escalation (LPE)`, hay nói dễ hiểu là leo thang đặc quyền, đây là blog của nó [PrintSpoofer](https://itm4n.github.io/printspoofer-abusing-impersonate-privileges/).
- Tối hôm đó, do 1 chút cố chấp ko muốn dùng privilege do ko quen (lúc đó mình cứ nghĩ là nó khó dùng cơ), mình thuần nhờ gemini giúp để thu thập thông tin, nhưng kết quả ko đạt được gì cả.
- Sau hơn 1 tiếng lãng phí thì mình bắt đầu nghĩ tới việc tự tạo 1 lab Windows server có RCE để test. Và vì nó tốn chi phí để duy trì nên mình đã nghĩ cần phải nhanh chóng tranh thủ cơ hội thực hành có 102 này để gain experience.

> ### Note: Thực ra cũng trong tối hôm này, mình đã báo cáo lại với bên đó về vuln, gửi clip báo cáo cách mà web bị RCE, đồng thời cũng đã nhận phản hồi lại. Nhưng do sự chậm trễ trong việc fix nên thành ra mình mới có ngày thứ 2,3,... để ngồi exploit tiếp =)))

## Day 2:

### Phase 4: Gaining access
- Sáng do bận học, môn này ko được mở máy nên mình chưa làm được gì cả.
- Đến chiều hôm đó, mình nán lại giảng đường, và định bụng đợi đến tầm 1h mới đi ăn trưa, đó cũng chính là lúc mình bắt đầu mở máy và ngồi nghiền ngẫm blog kia. Do nó cuốn quá nên mình lỡ ngồi liền mạch luôn đến gần 5r mới về, may trong cặp vẫn còn bánh ăn lót dạ:))
- Trong buổi chiều đó, mình đã đạt được khá nhiều thứ:
    - Đầu tiên, trên lab tự lab tự setup của mình thì PrintSpoofer đã bị chặn bởi Windows Defender và ko thể tải xuống được (nó liên tục bị auto delete)
    - Tuy nniên, sau đó mình thử encode bằng base64 thành text sau đó mới tải lên server rồi mới decode. Thực ra thì cách này cũng vẫn bị Windows Defender bắt được và ko thành công.
    - Sau đó, mình còn thử 1 số cách để bypass như là sửa các tên gọi hàm cùng với các code trong đó, thêm thông tin dư thừa, làm rối nhưng với lab tự setup thì vẫn bị Windows Defender bắt được 🤨.
    - Mặc dù mình nhận ra lúc thử tắt Windows defender thì ko bị sao cả và bypass qua bình thường, nhưng do ko rõ điều kiện của web server kia thế nào nên mình hơi rối chút.
    - Sau vài tiếng thử, đồng hồ đã chỉ hơn 4h chiều, lúc này mình bắt đầu thấy nản và bắt đầu hơi liều một chút, thử download thẳng lên server luôn. Và kết quả nhận lại bất ngờ vcl:))), nó thành công thật luôn ạ.
    - Nhờ đó mình đi đến 1 kết luận là khả năng server đã tắt Windows Defender do trong quá trình code, 1 số file .asp đã bị xóa do sự nhầm lẫn là malicious code. Chắc admin phải cay lắm nên mới tắt đi hoặc vì lý do nào đó:))
- Với thu hoạch trong tay, mình khá vui khi đã lần đầu tiên thành công trong việc leo thang đặc quyền trên Windows.

- Nắm quyền sinh sát trong tay🤡, mình tự tin dạo 1 vòng quanh server tiếp, và lần này thì ko bị hạn chế bởi gì nữa.
- Mình bắt đầu check thử toàn bộ ổ C, quét thử xem có file .config hay file gì sensitive ko. Nhưng lúc đó mình thấy cả những folder của user administrator và các user khác cũng đều ko chứa 1 file nào liên quan đến code cả.
- Đến lúc này, mình hỏi gemini thường xuyên hơn, nhưng do 1 số thứ misleading trong response của gemini đã khiến mình lầm tưởng rằng server ko có code, và code có thể được sử dụng thông qua 1 vị trí khác ngoài ổ cứng (đây là do tôi ko quen thôi;)), lúc đó kể cả database cũng ko thao tác được do ko có được credential, password để xác thực.
- Thực sự thì tối hôm đó hơi ác mộng 1 chút, có quyền trong tay nhưng lại ko thể tìm thấy thứ thực sự có giá trị làm tôi ngồi prompt cả tối hôm đó mà lại tiếp tục thu được rất ít thông tin.

## Day 3:

### Phase 5: Explore
- Dù tối hôm qua ngủ trễ nhưng sáng nay mình vẫn dậy khá sớm, lúc này tinh thần đã lên cao và sẵn sàng chiến tiếp.
- Lúc này đầu óc đã tỉnh táo và thông suốt hơn 1 chút, mình bắt đầu thử lần mò vào Recycle bin, quả nhiên trong đó có 1 số sensitive data.
- Mình lần mò vào trong đó và restore lại cả folder, ngoại trừ 1 số file đặc biệt như desktop.ini hay gì đó (mình cũng ko rõ chức năng mấy cái này lắm nhưng nó bị lỗi khi cố restore lại).
- Sau đó mình check thấy 1 số file .asp trong đó, cùng 1 số thứ linh tinh như logs, hay gì đó đi kèm.
- Check một hồi lâu, đến tận tầm chiều, mình đã nghĩ đến việc tải nó về máy local cho dễ xem, cũng như backup trong trường hợp web fix vuln. Mình thử zip lại, cố gửi qua server của máy khác bằng cách tạo 1 http.server tạm trên Kali Linux, nhưng sau đó là sự thất vọng khi dù cố kiểu gì cũng bị Nginx chặn đứng lại, ko cho phép gửi đi.
- Sau đó, gemini đã cho mình 1 idea khá hay đó là việc encode thành base64 rồi sau đó copy về local mới decode lại. Nhưng idea này lại gặp 1 vấn đề khác nghiêm trọng là, đoạn text của base64 quá dài và ko thể hiển thị ra màn hình của web trong 1 lần bằng `type`.
- Xong mình lại có 1 idea khác đó là chia nhỏ file thành các chunk để copy về máy, và kết quả là tập .zip đầu tiên đã copy thành công sau khi chia thành 27 file text (và tôi phải ngồi cóp tay 🥲), và bất ngờ là file zip đó chỉ nặng chưa đầy 10MB (toibatlucqua).

- Tối hôm đó, trong lúc đi ăn mình đã nghĩ tới 1 khả năng, có khi nào có nhiều hơn 1 ổ cứng trong máy ko nhỉ? Thế là mình ăn vội rồi chạy tót lên phòng. Và quả thật trời ko phụ người, cuối cùng thì mỏ vàng thật sự đã hiện ra trước mắt.
- Toàn bộ source hay các sensitive data đều nằm trong này. Và cả tối mình chỉ ngồi đọc và xem qua việc hiển thị code của từng file qua shell.
- Thực sự thì mình cũng muốn tải về lắm, nhưng khi xem dung lượng của các folder chứa code và các data khác, nó có thể lên đến vài trăm MB, mà bản thân mình lại ko muốn download thủ công từng file lẻ, tốn sức mà ko hiệu quả.

> ### Note: Web đã được trang bị nginx cẩn thận giúp tránh được directory traversal

- Sau 1 hồi lần mò các file, mình thấy 1 folder dẫn tới nơi upload avatar của user, chợt mình nảy ra 1 ý tưởng khá táo bạo, đó là nếu user có thể truy cập đc file trong folder này, thì tại sao ta ko lợi dụng chính nó để download file về.
- Nhờ có idea mới, mình bắt tay vào làm, thử zip lại xong sau đó ném vào folder chứa upload avatar.
- Và kết quả sau đó nó thực sự thành công đúng như mong đợi, tiếp tục bám vào injection đó, mình lại tải tiếp các folder khác về máy.

> ### Note: Thực sự thì mình đã nghĩ rằng đã nắm trong tay tất cả rồi và sẽ ko có Day 4 luôn, nhưng có trong tay source code mà ko có được database thì cũng chưa gọi là thành công lắm.

## Day 4:

## Phase 6: Exploit
- Do mệt mỏi từ các hôm trước tích tụ nên mình đã ngủ hết buổi sáng, một phần cũng do mình nghĩ đã nắm trong tay đủ thứ cần thiết rồi nên hơi thả lỏng.
- Đến đầu giờ chiều cũng là lúc mình mở source code ra để xem, và thứ làm mình chú ý đến là trong file config.asp có chứa credential cũng như password để truy cập database.
- Lúc đó mới thật sự là vớ được kho báu, mình bắt đầu nhờ gemini generate ra một đoạn webshell viết bằng .asp để injection vào web thông qua upload avatar.
- Đó cũng chính là lúc mình bắt đầu tìm cách tải toàn bộ database xuống và nắm trong tay quyền sinh sát thật sự.
- Nhưng có 1 vấn đề mới xuất hiện, đó là password của user ko được lưu dưới dạng text mà được băm qua hàm MD5. Lúc này mình vẫn chưa biết gì về cách reverse hash MD5, thứ duy nhất mình biết về nó là hay dùng để lưu password giống bcrypt, argon2 nhưng được đánh giá là yếu hơn.
- Lúc này mình thử research về đủ thứ liên quan về nó, như các cách bruteforce, rainbow table hay hash collision attack. Nhưng đa phần tool mình tìm được ko có custom MD5 mà thuần bruteforce hay pre compute nên vẫn ko đủ tốt để sử dụng.
- Và lúc này mình thử hỏi gemini xem có tool nào trên kali linux hữu ích ko, và cuối cùng cũng tìm ra được chân ái, đó chính là John the Ripper. Đây là lần đầu tiên mình sử dụng nó, và thấy nó khá hữu dụng khi hỗ trợ cả custom cho các cách hash khác nhau. Do nắm trong tay source code nên mình còn sở hữu cả Salt và cách nó hash MD5. Nhờ đó quá trình diễn ra khá nhanh.
- Và kết quả là chỉ sau 1 đêm treo máy, 10 tiếng, ko GPU, mình đã crack được hơn nửa số pass.

### 🫡Đến đây thì kết quả đã thật sự ngã ngũ.
## Bài học rút ra:
- Hãy kiểm thử cẩn thận vì đôi khi lỗi lớn nhất ở đây lại là human chứ ko phải ở hệ thống.
- Nguyên tắc "Zero trust user input" phải luôn được áp dụng triệt để, không được lơ là.
- Nên thêm các quy tắc đặt password phức tạp, người dùng thường để dạng ngắn cho dễ nhớ nên việc hash MD5 ko có quá nhiều tác dụng.
- Sử dụng các phương pháp hiện đại để băm password thay vì MD5 đã lỗi thời.
- Hãy bật Windows Defender để nó detect các file malicious qua signature hoặc cứ tắt đi cũng đc.
- Hãy bật Windows Defender để nó detect các file malicious qua signature hoặc cứ tắt đi cũng đc.
- Hãy bật Windows Defender để nó detect các file malicious qua signature hoặc cứ tắt đi cũng đc.

> ### Cái quan trọng hoặc ko quan trọng nhắc lại 3 lần cho nhớ:)


## Final Thoughts: The Lesson Beyond the Shell

> Looking back at this 4-day journey, the most important thing I learned wasn't just how to bypass a filter or how to use a specific exploit like PrintSpoofer. It was about persistence.

> In the world of cybersecurity, the "Eureka" moment rarely happens at the start. It happens at 3:00 AM, after dozens of failed attempts, when you’re about to give up but decide to try just one more payload. From a simple avatar upload to gaining full control over a Windows Server, this experience taught me that security is a cat-and-mouse game where the smallest human error—like leaving a front-end script exposed or forgetting to re-enable a defender—can lead to a total compromise.

> As I close this log, the shell is gone and the vulnerability is (hopefully) patched, but the mindset remains: Never trust user input, and never stop exploring.

🤡 These English is what I really thought but I got this also from Gemini. Best support.