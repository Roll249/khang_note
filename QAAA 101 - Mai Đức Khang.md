# Máy tính hiện đại 

Máy tính để bàn hay điện thoại thông minh hoạt động với 2 trạng thái là 0 hoặc 1, nếu điện áp thấp thì là 0, điện áp cao thì là 1

>Bit là đơn vị nhỏ nhất của thông tin, bất kì kiểu dữ liệu nào hình ảnh, video hay văn bản đều có thể biểu diễn (có thể là rất dài), Sức mạnh của máy tính là khả năng tương tác với các bit này

> Để máy tính tạo ra kết quả hữu ích và chạy chương trình, chúng cần một cách để xử lý và thao tác các bit này. Vì mục đích này, máy tính có các cổng logic. Một cổng logic có một hoặc nhiều đầu vào để nhận tín hiệu và một hoặc nhiều đầu ra để truyền tín hiệu. Các cổng logic thuờng được biểu diễn bằng hình vuông chữ nhật 
> Ví dụ như 1 Not -> 0

## Các Mạch điện, vì một cổng logic đơn lẻ không thực sự hữu ích

Nhiều cổng logic kết hợp với nhau tạo ra thành một mạch điện và do đó nền tảng của máy tính như chúng ta biết ngày nay. 
Mạch điện có thể biểu diễn bằng sơ đồ tương tự các nốt nhạc, một nhạc sĩ bắt đầu từ bên trái tại thời điểm 0 và tiến triển theo thời gian bằng cách đọc sang bên phải.
![[Pasted image 20260302210123.png]]

Tương tự, mạch điện biểu diễn các thao tác cổng logic dưới dạng một chuỗi các thời điểm. Giống như bản nhạc, sơ đồ được đọc từ trái sang phải. Mạch điện này có 3 thời điểm và sử dụng 2 bit:

![[Pasted image 20260302210142.png]]

Đọc từ trái sang phải, ta có thể quan sát những gì xảy ra ở mỗi thời điểm. Bắt đầu từ cột đầu tiên cả hai bit đều được khởi tạo giới giá trị 0. Ở cột 1, một cổng logic chỉ được áp dụng chot bit đầu tiên. Không có gì xảy ra với bit thứ 2. Cổng logic ở bước thứ hai xử lý bit 1 và 2 cùng nhau và cho ra hai đầu ra. Ở bước thứ ba, một cổng logic được áp dụng riêng biệt cho từng bit.

## Cổng NOT

>	!
	 Cổng logic NOT là một cổng logic quen thuộc. 

Một cách để hiểu cách thức hoạt động của các cổng logic là sử dụng bảng chân lý. Bảng chân lý mô tả cách một cổng logic hoạt động với mỗi đầu vào. Để làm điều này, người ta thường viết ra đầu ra tương ứng cho mỗi tổ hợp đầu vào có thể.

Vì chỉ nhận một bit làm đầu vào, bảng chân lý của cổng NOT chỉ gồm hai dòng:

|Đầu vào|Đầu ra|
|---|---|
|0|1|
|1|0|

Nếu đầu vào là 0, đầu ra sẽ là 1 và ngược lại.

## $>=$ 1 cổng 

![[Pasted image 20260302210217.png]]


**Kết quả cho ra là 0

![[Pasted image 20260302210230.png]]

**Kết quả cho ra là 1

! Ta thấy rảng qua cổng >=1 ít nhất 1 bit có giá trị lớn hơn hoặc bằng 1 thì sẽ cho ra kết quả là 1 còn lại sẽ cho kết quả bằng 0

## Cổng NAND
Giỏi lắm! Nó sẽ biến đầu ra thành 1 nếu ít nhất một đầu vào là 0. Cổng này thực chất được gọi là cổng NAND (Not AND). Đây là cổng duy nhất cần thiết để thực hiện mọi phép tính mà một máy tính truyền thống có thể làm được.
***
# Từ Điện toán hiện đại đến điện toán lượng tử

Trong khi máy tính trên bàn làm việc của chúng ta tuân theo các định luật **vật lý cổ điển** và do đó đôi khi cũng được gọi là **máy tính cổ điển**, thì máy tính lượng tử tuân theo các quy tắc hoàn toàn khác - các quy luật của vật lý lượng tử. 
Vật lý lượng tử bắt nguồn từ những khám phá khoa học cho thyaas các nguyên tử và electron hoạt động hoàn toàn khác so với vật thể ở kích thước con người. Nhưng chúng ta sẽ tập trung không nhiều vào khía cạnh vật lý, mà vào cách hành vi này có thể được sử dụng trong điện toán lượng tử.
***
# Qbit 

> Thay vì là bit, đơn vị nhỏ nhất của thông tin lượng tử là bit lượng tử hay qubit. Chúng tượng tự như bit ở chỗ có hai trạng thái đo được, thường được gọi bằng $|0\rangle$ và $|1\rangle$. Tuy nhiên, cũng có những điểm khác biệt đáng kể, mà chúng ta sẽ tìm hiểu ở phần tiếp theo.

Để đạt được mục tiêu này, chúng ta sẽ khám phá cách thao tác các qubit bằng máy tính lượng tử. Cũng như máy tính cổ điển, chúng ta có thể mô tả máy tính lượng tử bằng các cổng lượng tử này, chúng ta sẽ hiểu rõ hơn về các qubit và cách thức hoạt động của máy tính lượng tử.
Hiện tại chúng ta chưa có sự hiểu biết trực quan về qubit, nhưng chúng ta sẽ xây dựng được một qubit trong quá trình này!

## Cổng X

Khám phá những tác động của Xcổng trên qubit bằng cách  
• thay đổi giá trị đầu vào từ $|0\rangle$ ĐẾN $|1\rangle$   
• và thêm một cái thứ hai X-cổng  
và thực hiện một phép đo mỗi lần.

![assets/gif/x-gate.gif](https://www.iqmacademy.com/curriculum/assets/gif/x-gate.gif)    

###### Gợi ý

- Mỗi dòng đại diện cho một qubit. Thay đổi giá trị đầu vào bằng cách nhấp vào các số ở đầu mỗi dòng.
- Các vị trí khác nhau (1, 2, 3, 4) mà cổng có thể được đặt biểu thị các bước thời gian khác nhau mà máy tính lượng tử có thể thực hiện một phép toán.
- Thực hiện phép đo bằng cách nhấp vào _nút Đo_ .
- Sử dụng thao tác kéo và thả để di chuyển, thêm hoặc xóa các cổng lượng tử.

[Thử nghiệm ở link này](https://roll249.github.io/khang_note/quantum-circuit.html)


XCổng này hoạt động rất giống với cổng NOT trong máy tính cổ điển. Thậm chí đôi khi nó còn được gọi là cổng NOT lượng tử.

***
# Đưa lượng tử vào cuộc thảo luận - Cổng Hadamad

Cổng X hoạt động rất giống với cổng NOT của máy tính cổ điển. Trên thực tế đôi khi nó còn được gọi là cổng NOT lượng tử. Cho đến hiện tại khi bạn đọc tài liệu này, nó không khác nhiều so với máy tính cổ điển. Vậy điều gì thực sự làm cho máy tính lượng tử trở nên khác biệt?

## Khám phá cổng Hadamard H

Qubit có một số đặc tính mà bit không có. Để nghiên cứu điều này chi tiết hơn, chúng ta sẽ khám phá cổng H. nó cho chúng ta cái nhìn đầu tiên về những gì làm cho máy tính lượng tử trở nên đặc biejet. Và để thực sự thấy được điều này, chúng ta cần thực hiện nhiều phép đo.

> !  Khám phá tác dụng của cổng Hadamard. HKiểm tra trạng thái của qubit bằng cách thử các giá trị đầu vào khác nhau và thực hiện ít nhất **10 phép đo** cho mỗi giá trị đầu vào. Sau đó, làm tương tự nhưng với...X kiểm tra và so sánh kết quả.

###### Gợi ý

- Mỗi dòng đại diện cho một qubit. Thay đổi giá trị đầu vào bằng cách nhấp vào các số ở đầu mỗi dòng.
- Thực hiện phép đo bằng cách nhấp vào _nút Đo_ .
- Sử dụng thao tác kéo và thả để di chuyển, thêm hoặc xóa các cổng lượng tử.
  
[Thử nghiệm ngay ở đây](https://roll249.github.io/khang_note/hadamard.html)

***
# Sự chồng chập
Trong khi cổng **X** này trông không quá mới, bạn có thể đã nhận thấy đó gọi là cổng Hadamard. Cổng **H** đảm bảo rằng chúng ta đo lường $|0\rangle$ trong khoảng 50% trường hợp và $|1\rangle$ trong 50% còn lại
Việc áp dụng cổng Hadamard do đó đưa qubit vào trạng thái mà trong đó xác xuất đo được $|0\rangle$ hoặc $|1\rangle$ trong mỗi trường hợp, tỉ lệ này là 50%. Như vậy, bạn đã khám phá ra một trong những đặc điểm đặc biệt của thế giới máy tính lượng tử:

> Một qubit có thể ở trạng thái $|0\rangle$, trong trạng thái $|1\rangle$ hoặc ở trong trạng thái chồng chập của $|0\rangle$ hoặc $|1\rangle$. Khi đó, nó có 1 xác suất nhất định để được đo lường như vậy ($|0\rangle$ hoặc $|1\rangle$). Tuy nhiên, phép đo sẽ phá vỡ trạng thái chồng chập - tức là tất cả các phép đo tiếp theo sẽ cho cùng 1 kết quả như phép đo ban đầu

Điều này khác với cách hoạt động của bit, vì vậy chúng ta hãy thử hiểu rõ hơn bằng cách so sánh với màu sắc.

Hãy biểu diễn trạng thái **|0⟩** bằng màu đỏ <span style="color: red;">■</span> và trạng thái **|1⟩** bằng màu xanh dương <span style="color: blue;">■</span>. Một sự chồng chập (superposition) của **|0⟩** và **|1⟩** sẽ là một hỗn hợp của hai màu này. Nghĩa là, một màu nào đó trên quang phổ dưới đây:

<div style="position: relative; height: 12px; width: 100%; background: linear-gradient(90deg, #ff4444, #4444ff); margin: 30px 0 10px 0; border-radius: 6px;">
  <span style="position: absolute; left: 0; top: -25px; font-size: 1.2em; font-family: 'Times New Roman', serif;"> </span>
  <span style="position: absolute; right: 0; top: -25px; font-size: 1.2em; font-family: 'Times New Roman', serif;"> </span>
</div>
<div style="height: 20px;"></div>
$|0\rangle$                                                                                                                                                    $|1\rangle$ 

Một hỗn hợp 50-50 sẽ trông giống màu tím <span style="color: purple;">■</span>, nhưng nếu có một sự chồng chập không cân bằng, chúng ta sẽ thấy một màu khác. Ví dụ, nếu tôi trộn 80% xanh dương <span style="color: blue;">■</span> với 20% đỏ <span style="color: red;">■</span>, tôi sẽ được màu tím xanh <span style="color: blueviolet;">■</span>.

Đây là điểm khác biệt giữa thế giới lượng tử và thế giới cổ điển. Nếu tôi yêu cầu bạn đo màu của một hỗn hợp (tức là, một sự chồng chập của các màu) trong thế giới cổ điển, bạn có thể dễ dàng làm điều đó và nói rằng hỗn hợp là màu tím xanh <span style="color: blueviolet;">■</span> hoặc màu tím <span style="color: purple;">■</span>, v.v.

Tuy nhiên, trong cơ học lượng tử, một phép đo sẽ phá hủy sự chồng chập. Nếu bạn được yêu cầu đo một sự chồng chập của các màu trong cơ học lượng tử, câu trả lời của bạn sẽ luôn là màu đỏ <span style="color: red;">■</span> hoặc màu xanh dương <span style="color: blue;">■</span>, đây là hai màu cơ bản (trạng thái) của hệ thống. Tuy nhiên, tỷ lệ của hai màu vẫn quan trọng. Tỷ lệ này sẽ xác định tần suất bạn nhận được mỗi kết quả.

Vì vậy, nếu bạn đo màu của một trạng thái tím <span style="color: purple;">■</span>, 50% lần đo của bạn sẽ là màu xanh dương <span style="color: blue;">■</span> và 50% lần đo sẽ là màu đỏ <span style="color: red;">■</span>. Đối với màu tím xanh <span style="color: blueviolet;">■</span> (tức là 80% xanh dương <span style="color: blue;">■</span> và 20% đỏ <span style="color: red;">■</span>), bạn sẽ đo được màu xanh dương <span style="color: blue;">■</span> 80% lần và màu đỏ <span style="color: red;">■</span> 20% lần.

## Một mô hình cho qubit
 
Mô hình bóng đèn như đã trình bày ở phần Máy tính hiện đại không đủ cho các qubit, vậy nên chúng ta cần một cách khác để hình dung các qubit. Một cách thuận tiện để biểu diễn sự chồng chập, xác suất và hiệu ứng của phép đo là sử dụng hình cầu Bloch, mô tả bề mặt của một quả địa cầu với 2 cực bắc và nam đại diện cho... $|0\rangle$ $|1\rangle$

![Bloch][https://www.iqmacademy.com/curriculum/curriculum/qubits-2-messbare-zustaende.svg]
Để mô hình tư duy của chúng ta có thể nắm bắt được những gì chúng ta vừa trải nghiệm với cổng H, chúng ta có thể cho phép mũi tên chỉ theo hướng khác ngoài hướng lên hoặc hướng xuống. Ví dụ, sau khi áp dụng cổng Hadamard lên một qubit ở trạng thái $|0\rangle$, mũi tên sẽ hướng về phía xích đạo. Nếu chúng ta đo qubit, trạng thái của nó sẽ rơi về trạng thái cơ sở hướng lên (đại diện cho $|0\rangle$) hoặc trạng thái cơ sở hướng xuống (đại diện cho $|1\rangle$) với một xác suất nhất định.

Quá trình hoạt hình sẽ chuẩn bị một qubit ở trạng thái chồng chập đều của|0⟩Và|1⟩Trước mỗi lần đo, bạn có thể thử xem phép đo ảnh hưởng đến trạng thái của qubit như thế nào bằng cách nhấn nút _Đo_ .

[Thử ngay ở đây](https://roll249.github.io/khang_note/model.html)

Chuẩn bị một qubit bằng cổng Hadamard ở trạng thái chồng chập đều và đo nó như sau:|0⟩Trạng thái của qubit sau phép đo của chúng ta là gì?

Việc đo một qubit sẽ phá hủy trạng thái chồng chập của nó. Do đó, phép đo thứ hai sẽ lại cho kết quả như vậy. |0⟩.

***
# Thử Nghiệm qua 2 cổng Hadamard

Có vẻ như cổng Hadamard sẽ xóa mọi thông tin được lưu trữ trong qubit, bởi vì batass kì giá trị đầu là $|0\rangle$ hay $|1\rangle$, xác suất đo được một trong 2 đều là 50% trong cả hai trường hợp. Do đó, theo các phép đo mà chúng ta thực hiện, hai trạng thái dường như không thể phân biệt được. Nhưng ta đặt ra câu hỏi, liệu điều đó có thật sự đúng 
> Chúng ta sẽ thử áp dụng cổng H hai lần liên tiếp. Chuyển đổi giữa hai giá trị khác nhau $|0\rangle$ và $|1\rangle$

[Thử nghiệm ngay ở đây!](https://roll249.github.io/khang_note/doubleH.html)
Đúng vậy! Áp dụng Hadamard hai lần sẽ đưa qubit trở lại trạng thái ban đầu. Từ đó, ta có thể kết luận rằng thông tin được lưu trữ trong qubit không bị mất. Điều này có nghĩa là ngay cả khi kết quả đo có vẻ hoàn toàn ngẫu nhiên, qubit vẫn ở trong một trạng thái được xác định rõ ràng.

Điều này có nghĩa là chúng ta cần phân biệt các vị trí trên đường xích đạo của mặt cầu Bloch của chúng ta. Góc quay giờ đây trở thành một phần quan trọng của trạng thái lượng tử của chúng ta. Trạng thái thu được sau khi áp dụng cổng H lên một qubit ở trạng thái $|0\rangle$ là đối diện với trạng thái thu được sau khi áp dụng cổng H lên một qubit ở trạng thái $|1\rangle$.

Tất cả các phép toán sau đó có thể được xem như một phép quay quanh một trục nào đó của mặt cầu. Cổng X là một phép quay 180° quanh trục đi qua đường xích đạo. Cổng H là một phép quay quanh trục chéo nằm giữa trục x và trục z

[Thử nghiệm ngay ở đây](https://roll249.github.io/khang_note/doubleH.html)

[Nếu bạn có thắc mắc ở phần này hãy đọc thêm ở đây(Thông tin riêng về cổng Hadamard sẽ có một bài viết riêng)](https://postquantum.com/quantum-computing/hadamard-gate/)

***
# Giới thiệu thêm về các cổng khác 

Ta vừa đi qua cổng Hadamad 2 lần để đưa  qubit về trạng thái ban đầu. Bắt đầu từ đây chúng ta có thể kết luận rằng thông tin lưu trữ trong qubit không bị mất. Điều này có nghĩa ngay cả khi kết quả đo có vẻ hoàn toàn ngẫu nhiên, qubit vẫn đang ở một trạng thái được xác định rõ ràng
Cho đến nay, chúng ta mới chỉ khám phá ra bốn trạng thái khác nhau mà một qubit có thể tồn tại. Trên thực tế cả bốn trạng thái này đều đó đều có tên gọi, trong đó 2 trạng thái mới được gọi là: 

$|+\rangle$: trạng thái của một qubit sau khi cổng Hadamard được áp dụng cho qubit ở trạng thái $|0\rangle$
$|-\rangle$: trạng tái của một qubit sau khi cổng Hadamard được áp dụng cho qubit ở trạng thái $|1\rangle$

Cổng X không chỉ chuyển đổi trạng thái giữa $|0\rangle$ và $|1\rangle$, mà nói đúng ra thì cổng X xoay trạng thái của qubit quanh trục x.

[Thử ngay ở đây để quan sát rõ hơn](https://roll249.github.io/khang_note/gate.html)

## Kết hợp các cổng 

Tuy nhiên, cổng H và X không phải là các cổng logic duy nhất. Máy tính lượng tử có rất nhiều cánh cổng khác. Một trong số đó là cổng T, có thể coi là một phép quay 45 độ quanh trục cực bắc-nam của quả cầu(di chuột qua nút cổng để xem trục). Góc quay quanh trục cực bắc-nam này gọi là pha.

> Cổng T chưa được biết đến dường như không có tác dụng gì đối với $|0\rangle$ và $|1\rangle$. Chèn thêm cổng Hadamard H trước và sau cổng T. Điều này ảnh hưởng đến kết quả đo như thế nào?

[Thử nghiệm ngay ở đây](https://roll249.github.io/khang_note/gate.html)

***
# Xác suất đo được trạng thái $|1\rangle$ khi bắt đầu từ trạng thái $|0\rangle$ và kẹp một cổng **T** giữa hai cổng **H** là bao nhiêu?

> Nếu chúng ta áp dụng một cổng **H** trước và sau cổng **T**, chúng ta sẽ thu được kết quả đo là $|0\rangle$ với xác suất 85% khi giá trị bắt đầu là $|0\rangle$ và chỉ 15% là $|1\rangle$.
>
> Điều này có nghĩa là bất kỳ sự thay đổi nào của **pha** (ví dụ: bởi một cổng khác như cổng T), dù nhỏ đến đâu, cũng có thể được phát hiện bằng cách nhúng nó giữa hai cổng Hadamard. Việc chỉ áp dụng cổng Hadamard **H** trước cổng **T** vẫn sẽ thay đổi trạng thái của nó, nhưng sẽ không thể nhìn thấy trong kết quả đo.
>


***

Điều này cũng có nghĩa là các trạng thái lượng tử không thể chỉ được mô tả đơn thuần bằng xác suất, vì các trạng thái được tạo ra bởi một cổng **H** và bởi cổng **H** theo sau là cổng **T** sẽ cho kết quả đo giống nhau mặc dù chúng khác nhau.

Bạn sẽ đôi khi gặp ký hiệu sau đây được gọi là **ký hiệu ket**:
$$ \sqrt{0.85}|0\rangle + \sqrt{0.15}|1\rangle $$

Các hệ số liên quan đến các kết quả đo khác nhau $|0\rangle$ và $|1\rangle$ được gọi là **biên độ** (amplitudes). Xác suất để thu được một trong các kết quả được cho bởi bình phương của biên độ, ví dụ: $P(|0\rangle) = (\sqrt{0.85})^2 = 0.85$.

Ngoài ra, các trạng thái $|+\rangle$ và $|-\rangle$ cũng có thể được viết theo cách này:

*   $|+\rangle = \frac{1}{\sqrt{2}}|0\rangle + \frac{1}{\sqrt{2}}|1\rangle$
*   $|-\rangle = \frac{1}{\sqrt{2}}|0\rangle - \frac{1}{\sqrt{2}}|1\rangle$

Trong mô-đun này, chúng tôi không muốn đi sâu quá nhiều vào vấn đề này, nhưng nếu bạn quan tâm muốn tìm hiểu thêm, hãy kiểm tra phần **từ vựng (glossary)** hoặc đón chờ chương trình mở rộng của chúng tôi dành cho các "pháp sư mạch lượng tử" (circuit magicians).


[Thử nghiệm ngay ở đây](https://roll249.github.io/khang_note/gate.html)

***
# Càng nhiều qubit, càng thú vị hơn

Dĩ nhiên, một máy tính lượng tử chỉ với một qubit không thể thực hiện các phép tính phức tạp và thiết thực. Điều chúng ta cần là nhiều qubit hơn. Khái niệm chồng chập cũng áp dụng cho nhiều qubit

## Một mạch với 2 quibit ?

> Dưới đay là một mạch điện với hai qubit. Bạn có thể tạo ra bao nhiêu trạng thái từ sự chồng chập giữa chúng

[Thử ngay ở đây](https://roll249.github.io/khang_note/qubits.html)

Sau khi thử nghiệm ta kết luận rằng sự chồng chập giữa số lượng trạng thái mà chúng ta có thể tạo ra với hai qubit.

Có bốn qubit $|00\rangle$, $|01\rangle$, $|10\rangle$ và $|11\rangle$ . Để thu được các phép đo này, hãy áp dụng cổng H vào qubit thứ nhất và thứ hai và thực hiện một số phép đo.

## Thêm một cổng nữa xem sao

Chúng ta cần một cổng hoạt động đồng thời với cả 2 qubits.

> Dưới đây là một cổng logic hoạt động với hai qubit. Hãy đo mạch nhiều lần với các giá trị đầu vào khác nhau để khám phá tác dụng của cổng logic.

[Thử ngay ở đây](https://roll249.github.io/khang_note/qubits.html)

Nếu qubit điều khiển (trong ví dụ này là qubit trên) ở trạng thái $|1\rangle$, trạng thái của qubit đích (trong ví dụ này là qubit dưới) sẽ thay đổi, tức là $|0\rangle$ trở thành $|1\rangle$ và $|1\rangle$ trở thành $|0\rangle$. Nếu qubit điều khiển ở trạng thái $|0\rangle$, thì không có gì xảy ra với qubit đích. Đó là lý do tại sao cổng này còn được gọi là cổng NOT điều khiển (CNOT).


***
# Tổng hợp lại tất cả 

Cổng logic hai qubit đầu tiên mà chúng ta vừa học được gọi là cổng CNOT (Controlled NOT). Nó áp dụng một Xcổng đến qubit đích (trong hình bên dưới, qubit thứ hai) khi và chỉ khi qubit điều khiển (trong hình bên dưới, qubit thứ nhất) ở trạng thái $|0\rangle$ Nếu qubit điều khiển ở trạng thái $|1\rangle$ Nó chẳng có tác dụng gì cả.

![CNOT][https://www.iqmacademy.com/curriculum/curriculum/CNOT.svg]
Nhưng điều gì sẽ xảy ra nếu qubit điều khiển không ở trạng thái $|0\rangle$ hoặc $|1\rangle$nhưng ở trạng thái chồng chập?

#### Kết hợp CNOT và Hadamard

> Áp dụng cổng Hadamard H vào qubit đầu tiên trước khi áp dụng cổng CNOT để đưa nó vào trạng thái chồng chập đồng nhất. Sau đó thực hiện lại một số phép đo với các giá trị đầu vào khác nhau.

[Thử ngay ở đây](https://roll249.github.io/khang_note/together.html)

Bắt đầu với trạng thái ban đầu là $|0\rangle$ cho cả hai qubit. Nếu ta đo được qubit thứ nhất là $|1\rangle$, thì việc đo qubit thứ hai như thế nào là chính xác?

Nó sẽ được đo được là $|1\rangle$
Nếu qubit điều khiển ở trạng thái chồng chập, kết quả đo của qubit mục tiêu phụ thuộc vào giá trị mà qubit điều khiển cuối cùng sẽ được đo. Do đó, trạng thái của hai qubit bị vướng víu với nhau. Nếu đo trạng thái của một trong hai qubit, trạng thái của qubit còn lại sẽ tự động được biết.

***
# Rối - Một mối liên kết trọn đời

Như bạn đã trải nghiệm qua các phép đo, kết quả của một mạch trong máy tính lượng tử thường không cố định ngay từ đầu. Cho dù chúng ta có bao nhiêu qubit đi chăng nữa, khi thực hiện phép đo, chúng ta chỉ nhận được một kết quả cụ thể với một xác suất nhất định. Để có được kết quả đáng tin cậy về trạng thái và để có thể phân biệt giữa các trạng thái chồng chập khác nhau, chúng ta lại thực hiện một số lượng lớn phép đo để xác định chính xác xác suất này 

Điều này trở nên thú vị khi liên quan tới cổng CNOT: Neues qubit [Thử ngay ở đây]()điều khiển ở trạng thái chồng chập, kết quả đo của qubit mục tiêu phụ thuộc vào giá trị mà qubit điều khiển đang nhận. Từ đó, chúng ta đã hiểu thêm một đặc điểm đặc biệt khác của qubit:

> Hai qubit có thể rối với nhau. Trạng thái rối không thể được biểu diễn bằng một trạng thái riêng cho mỗi qubit. Tuy nhiên, nếu ta đo trạng thái của một trong hai qubit, ta sẽ tự động biết được trạng thái của qubit còn lại.

Hiện tượng rối tử cho phép chúng ta tạo ra những trạng thái mà chúng ta không thể đạt được bằng bất kỳ cách nào khác – ví dụ, một trạng thái mà chúng ta đo lường được $|00\rangle$ trong 50% thời gian $|11\rangle$ trong 50% thời gian

> Sử dụng tất cả các cổng logic có sẵn để tạo ra trạng thái có xác suất 50% nhận được $|10\rangle$ làm đầu ra và xác suất 50% nhận được $|01\rangle$ làm đầu ra.

- Bạn có thể tạo cổng CNOT bằng cách kéo ô CX vào

[Thử ngay ở đây](https://roll249.github.io/khang_note/CX.html)

Liệu trạng thái có xác suất 50% nhận được 10⟩ làm đầu ra và xác suất 50% nhận được 01⟩ làm đầu ra cũng là một trạng thái vướng lượng tử?

Câu trả lời là có Khi đo một qubit và nhận được giá trị 0, ta đã biết qubit còn lại sẽ được đo là 1.

![cnot mix][https://www.iqmacademy.com/curriculum/assets/img/foundations/hadamard-not-cnot.png]

[Giải Nobel Vật lý năm 2022 ](https://www.nobelprize.org/prizes/physics/2022/press-release/)được trao cho Alain Aspect, John F. Clauser và Anton Zeilinger vì công trình nghiên cứu về sự vướng víu lượng tử. Các công cụ thực nghiệm mà họ phát triển đã giúp hiện thực hóa điện toán lượng tử và các công nghệ lượng tử khác.

***
# Máy tính lượng tử 5 qubit

Hãy cùng tìm hiểu sâu hơn về máy tính lượng tử 5 qubit. Sử dụng cổng Hadamard và CNOT, chúng ta có thể tạo ra trạng thái mà trong đó ta đo được toàn số 0 hoặc toàn số 1. Đây là trạng thái vướng lượng tử tối đa. Và nó thậm chí còn có một tên gọi đặc biệt: trạng thái GHZ (được đặt theo tên ba người: Greenberger, Horne và Zeilinger). Trạng thái này có thể được sử dụng để đánh giá mức độ hoạt động hiệu quả của sự vướng lượng tử trên một thiết bị lượng tử thực tế. Xem hình bên dưới để biết thiết bị 5 qubit thực tế do IQM Quantum Computers chế tạo.

> Hãy tạo trạng thái GHZ (Greenberger, Horne và Zeilinger) cho 5 qubit! Chúng ta cần ít nhất bao nhiêu cổng logic để tạo ra trạng thái này?

[Thử ngay ở đây](https://www.iqmacademy.com/curriculum/foundations10.html)

***

# Sức mạnh của tính toán lượng tử

Chúng ta đã thấy rằng tính toán lượng tử khác biệt với tính toán cổ điển ở rất nhiều khía cạnh. Sức mạnh của máy tính lượng tử đối với một số vấn đề nhất định trở nên rõ ràng khi chúng ta so sánh chúng với đối tác cổ điển của chúng. Trong khi một máy tính cổ điển với $n$ bit chỉ có thể biểu diễn một trạng thái duy nhất, thì một máy tính lượng tử với $n$ qubit có thể biểu diễn $2^n$ trạng thái đồng thời. Ví dụ, một máy tính thông thường với 2 bit có thể ở trạng thái $|10\rangle$, do đó biểu diễn số 2. Tuy nhiên, một máy tính lượng tử với 2 qubit có thể ở trạng thái **chồng chập** (superposition) của các trạng thái $|00\rangle, |01\rangle, |10\rangle$ và $|11\rangle$, do đó biểu diễn các số 0, 1, 2 và 3 đồng thời.

*(Biểu đồ minh họa sự tăng trưởng theo hàm mũ của số lượng số được biểu diễn đồng thời khi số lượng qubit tăng lên)*

> Một siêu máy tính khổng lồ có dung lượng bộ nhớ để lưu trữ khoảng 50 qubit. Việc lưu trữ 51 qubit đã sẽ yêu cầu đến hai cỗ máy như vậy. Với một máy tính lượng tử, chúng ta chỉ cần thêm một qubit. Đi xa hơn nữa, chỉ cần 300 qubit để biểu diễn nhiều số đồng thời hơn cả số lượng nguyên tử trong vũ trụ.

Trong khi máy tính cổ điển chỉ ở trong một trạng thái và chỉ xử lý trạng thái đó, thì máy tính lượng tử cho phép hoạt động trên một trạng thái chồng chập biểu diễn nhiều kết quả. Nhưng chỉ một kết quả duy nhất có thể thu được qua một phép đo. Do đó, **chúng ta cần các thuật toán thông minh tận dụng được những đặc thù của qubit (cụ thể là sự chồng chập và vướng víu) và đảm bảo rằng kết quả đo chính xác trở nên rất có khả năng xảy ra.**

Trong thực tế, các kết quả chính xác hiếm khi được đo với xác suất 100%. Do đó, một chương trình trên máy tính lượng tử cần được thực thi thường xuyên hơn so với trên máy tính thông thường và thường sẽ đưa ra một dải các câu trả lời thay vì một câu trả lời đơn lẻ.

Việc thu được nhiều câu trả lời khả dĩ từ một máy tính lượng tử nghe có vẻ kém chính xác hơn so với kết quả mà chúng ta thu được từ máy tính thông thường. Với phép cộng 44 và 32, chúng ta không muốn câu trả lời là "có khả năng nhất là 76", mà phải là chính xác 76. Không ngạc nhiên khi, đối với các tác vụ như chạy máy chủ web hoặc phép cộng, chúng ta hầu như sẽ không thấy máy tính lượng tử thay thế máy tính thông thường.

Tuy nhiên, có nhiều loại vấn đề mà việc giảm số lượng câu trả lời khả dĩ xuống chỉ còn một vài kết quả sẽ mang lại sự tiết kiệm thời gian rất lớn. Trong mô-đun tiếp theo, bạn sẽ tìm hiểu xem những trường hợp sử dụng đó trông như thế nào.
***


Cảm ơn bạn đã đọc tới đây!
Các kiến thức do mình biên dịch và tổng hợp diễn giải từ 2 nguồn sau các bạn có thể tham khảo:
https://www.iqmacademy.com/curriculum
https://postquantum.com/quantum-computing/hadamard-gate/
Lời cảm ơn đặc biệt đến thầy ThS NGUYỄN PHƯƠNG NAM đã cho em động lực học tập về quantum computing: https://scholar.google.com/citations?user=FON17ToAAAAJ&hl=en

Các kiến thức đến từ sự chủ quan của mình khi tìm hiểu nên có sai sót kính mong có được sự góp ý và thông cảm của các bạn!