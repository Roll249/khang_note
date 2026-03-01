# Máy tính hiện đại 

Máy tính để bàn hay điện thoại thông minh hoạt động với 2 trạng thái là 0 hoặc 1, nếu điện áp thấp thì là 0, điện áp cao thì là 1

>Bit là đơn vị nhỏ nhất của thông tin, bất kì kiểu dữ liệu nào hình ảnh, video hay văn bản đều có thể biểu diễn (có thể là rất dài), Sức mạnh của máy tính là khả năng tương tác với các bit này

> Để máy tính tạo ra kết quả hữu ích và chạy chương trình, chúng cần một cách để xử lý và thao tác các bit này. Vì mục đích này, máy tính có các cổng logic. Một cổng logic có một hoặc nhiều đầu vào để nhận tín hiệu và một hoặc nhiều đầu ra để truyền tín hiệu. Các cổng logic thuờng được biểu diễn bằng hình vuông chữ nhật 
> Ví dụ như 1 Not -> 0

## Các Mạch điện, vì một cổng logic đơn lẻ không thực sự hữu ích

Nhiều cổng logic kết hợp với nhau tạo ra thành một mạch điện và do đó nền tảng của máy tính như chúng ta biết ngày nay. 
Mạch điện có thể biểu diễn bằng sơ đồ tương tự các nốt nhạc, một nhạc sĩ bắt đầu từ bên trái tại thời điểm 0 và tiến triển theo thời gian bằng cách đọc sang bên phải.

<svg version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px" viewBox="0 0 600 140" style="enable-background: new 0 0 600 140">
      <g class="clef">
        <rect x="190" y="20" class="st0" width="310" height="1"></rect>
        <rect x="190" y="40" class="st0" width="310" height="1"></rect>
        <rect x="190" y="60" class="st0" width="310" height="1"></rect>
        <rect x="190" y="80" class="st0" width="310" height="1"></rect>
        <rect x="190" y="100" class="st0" width="310" height="1"></rect>
        <rect x="498" y="20" class="st0" width="2" height="81"></rect>
        <rect x="190" y="20" class="st0" width="2" height="81"></rect>

      </g>
      <g class="beat doh">
        <path d="M258,24.5L258,24.5v65c0,0.3,0.2,0.5,0.5,0.5h1.2c0.3,0,0.5-0.2,0.5-0.5V29.8c3.3,1.7,9.4,1.4,14.2-1.2
                c6.6-3.6,10.2-10.1,7.9-14.6c-2.3-4.5-9.5-5.3-16.1-1.7C260.7,15.2,258,20.3,258,24.5z"></path>
      </g>
      <g class="beat ray">
        <path d="M338,64.5L338,64.5v65c0,0.3,0.2,0.5,0.5,0.5h1.2c0.3,0,0.5-0.2,0.5-0.5V69.8c3.3,1.7,9.4,1.4,14.2-1.2
                c6.6-3.6,10.2-10.1,7.9-14.6c-2.3-4.5-9.5-5.3-16.1-1.7C340.7,55.2,338,60.3,338,64.5z"></path>
        <path d="M338,24.5L338,24.5v65c0,0.3,0.2,0.5,0.5,0.5h1.2c0.3,0,0.5-0.2,0.5-0.5V29.8c3.3,1.7,9.4,1.4,14.2-1.2
                c6.6-3.6,10.2-10.1,7.9-14.6c-2.3-4.5-9.5-5.3-16.1-1.7C340.7,15.2,338,20.3,338,24.5z"></path>
        <polygon points="351,86.8 327,99 327,93.2 351,81"></polygon>
        <polygon points="351,96.8 327,109 327,103.2 351,91"></polygon>
        <polygon points="351,106.8 327,119 327,113.2 351,101"></polygon>
      </g>
      <g class="beat egon">
        <path d="M418,64.5L418,64.5v65c0,0.3,0.2,0.5,0.5,0.5h1.2c0.3,0,0.5-0.2,0.5-0.5V69.8c3.3,1.7,9.4,1.4,14.2-1.2
                c6.6-3.6,10.2-10.1,7.9-14.6c-2.3-4.5-9.5-5.3-16.1-1.7C420.7,55.2,418,60.3,418,64.5z"></path>
        <path d="M418,24.5L418,24.5v65c0,0.3,0.2,0.5,0.5,0.5h1.2c0.3,0,0.5-0.2,0.5-0.5V29.8c3.3,1.7,9.4,1.4,14.2-1.2
                c6.6-3.6,10.2-10.1,7.9-14.6c-2.3-4.5-9.5-5.3-16.1-1.7C420.7,15.2,418,20.3,418,24.5z"></path>
      </g>
    </svg>
Tương tự, mạch điện biểu diễn các thao tác cổng logic dưới dạng một chuỗi các thời điểm. Giống như bản nhạc, sơ đồ được đọc từ trái sang phải. Mạch điện này có 3 thời điểm và sử dụng 2 bit:

<svg version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px" viewBox="70 0 480 180" style="enable-background: new 0 0 600 180">
      <g class="clef">
        <path d="M398.6,29l0.9,0c0.3,0,0.6,0,0.9-0.1c0.3-0.1,0.5-0.2,0.7-0.4s0.4-0.4,0.5-0.6c0.1-0.2,0.2-0.5,0.3-0.8
                    c0-0.3,0-0.5,0-0.7s-0.1-0.4-0.3-0.6c-0.1-0.2-0.3-0.3-0.5-0.4c-0.2-0.1-0.4-0.1-0.7-0.2c-0.3,0-0.6,0-0.8,0.1s-0.5,0.2-0.6,0.4
                    c-0.2,0.2-0.3,0.4-0.5,0.6c-0.1,0.2-0.2,0.5-0.3,0.8l-1.3,0c0-0.5,0.2-0.9,0.4-1.2c0.2-0.4,0.5-0.7,0.8-0.9
                    c0.3-0.3,0.7-0.5,1.1-0.6c0.4-0.1,0.8-0.2,1.3-0.2c0.4,0,0.8,0.1,1.2,0.2c0.4,0.1,0.7,0.3,0.9,0.6c0.2,0.3,0.4,0.6,0.5,0.9
                    c0.1,0.4,0.2,0.8,0.1,1.2c0,0.3-0.1,0.6-0.2,0.8s-0.3,0.5-0.5,0.7c-0.2,0.2-0.4,0.4-0.6,0.5s-0.5,0.3-0.7,0.4
                    c0.2,0.1,0.5,0.2,0.6,0.4c0.2,0.2,0.3,0.4,0.4,0.6c0.1,0.2,0.2,0.4,0.2,0.7c0,0.2,0,0.5,0,0.8c0,0.5-0.2,0.9-0.4,1.3
                    c-0.2,0.4-0.5,0.7-0.8,1c-0.3,0.3-0.7,0.5-1.1,0.6c-0.4,0.1-0.9,0.2-1.4,0.2c-0.4,0-0.8-0.1-1.2-0.2s-0.7-0.3-0.9-0.6
                    c-0.3-0.3-0.5-0.6-0.6-0.9s-0.2-0.8-0.2-1.2l1.3,0c0,0.3,0,0.5,0.1,0.7c0.1,0.2,0.2,0.4,0.3,0.6c0.1,0.2,0.3,0.3,0.5,0.4
                    s0.5,0.1,0.7,0.1c0.3,0,0.6,0,0.9-0.1c0.3-0.1,0.5-0.2,0.7-0.4c0.2-0.2,0.4-0.4,0.5-0.7c0.1-0.3,0.2-0.5,0.2-0.9
                    c0-0.3,0-0.6-0.1-0.8s-0.2-0.4-0.3-0.6s-0.4-0.3-0.6-0.4s-0.5-0.1-0.8-0.1l-1,0L398.6,29z"></path>
        <rect x="250" y="130" width="180" height="1"></rect>
        <rect x="250" y="70" width="180" height="1"></rect>
        <path d="M224.4,71.6c-0.1,0.7-0.3,1.4-0.5,2.1s-0.6,1.3-1,1.8s-1,0.9-1.6,1.2s-1.3,0.5-2.2,0.4c-0.8,0-1.4-0.2-2-0.5
                    s-0.9-0.8-1.2-1.3s-0.5-1.1-0.5-1.8s-0.1-1.3,0-2l0.5-3.4c0.1-0.7,0.3-1.4,0.5-2.1s0.6-1.3,1-1.8s1-0.9,1.6-1.2s1.3-0.5,2.2-0.4
                    c0.5,0,1,0.1,1.4,0.3s0.7,0.4,1,0.6s0.5,0.6,0.7,0.9s0.3,0.7,0.4,1.1s0.2,0.8,0.2,1.3s0,0.9-0.1,1.4L224.4,71.6z M222.1,69.5
                    l-4.1,2.8c0,0.3,0,0.6,0,0.9s0,0.6,0.1,0.9s0.2,0.5,0.4,0.7s0.5,0.3,0.8,0.3c0.4,0,0.8-0.1,1-0.3s0.5-0.4,0.7-0.7s0.3-0.6,0.4-1
                    s0.2-0.7,0.2-1L222.1,69.5z M218.3,70.3l4.1-2.8c0-0.3,0.1-0.6,0.1-0.9s0-0.6-0.1-0.9s-0.2-0.5-0.4-0.7s-0.5-0.3-0.8-0.3
                    c-0.4,0-0.8,0.1-1,0.2s-0.5,0.4-0.7,0.7s-0.3,0.6-0.4,1s-0.2,0.7-0.2,1L218.3,70.3z"></path>
        <path d="M224.4,131.6c-0.1,0.7-0.3,1.4-0.5,2.1s-0.6,1.3-1,1.8s-1,0.9-1.6,1.2s-1.3,0.5-2.2,0.4c-0.8,0-1.4-0.2-2-0.5
                    s-0.9-0.8-1.2-1.3s-0.5-1.1-0.5-1.8s-0.1-1.3,0-2l0.5-3.4c0.1-0.7,0.3-1.4,0.5-2.1s0.6-1.3,1-1.8s1-0.9,1.6-1.2s1.3-0.5,2.2-0.4
                    c0.5,0,1,0.1,1.4,0.3s0.7,0.4,1,0.6s0.5,0.6,0.7,0.9s0.3,0.7,0.4,1.1s0.2,0.8,0.2,1.3s0,0.9-0.1,1.4L224.4,131.6z M222.1,129.5
                    l-4.1,2.8c0,0.3,0,0.6,0,0.9s0,0.6,0.1,0.9s0.2,0.5,0.4,0.7s0.5,0.3,0.8,0.3c0.4,0,0.8-0.1,1-0.3s0.5-0.4,0.7-0.7s0.3-0.6,0.4-1
                    s0.2-0.7,0.2-1L222.1,129.5z M218.3,130.3l4.1-2.8c0-0.3,0.1-0.6,0.1-0.9s0-0.6-0.1-0.9s-0.2-0.5-0.4-0.7s-0.5-0.3-0.8-0.3
                    c-0.4,0-0.8,0.1-1,0.2s-0.5,0.4-0.7,0.7s-0.3,0.6-0.4,1s-0.2,0.7-0.2,1L218.3,130.3z"></path>
        <path d="M223.3,30.9c-0.1,0.5-0.2,1.1-0.4,1.6c-0.2,0.5-0.5,1-0.8,1.4c-0.3,0.4-0.7,0.7-1.2,0.9
                    c-0.5,0.2-1,0.3-1.6,0.3c-0.4,0-0.7-0.1-1-0.2c-0.3-0.1-0.6-0.3-0.8-0.5c-0.2-0.2-0.4-0.4-0.5-0.7s-0.2-0.6-0.3-0.9
                    c-0.1-0.3-0.1-0.6-0.1-1s0-0.7,0.1-1l0.4-2.5c0.1-0.4,0.1-0.7,0.2-1.1c0.1-0.4,0.2-0.7,0.4-1c0.2-0.3,0.3-0.6,0.6-0.9
                    s0.5-0.5,0.7-0.7c0.3-0.2,0.6-0.3,0.9-0.4s0.7-0.2,1.1-0.1c0.4,0,0.7,0.1,1,0.2c0.3,0.1,0.6,0.3,0.8,0.5c0.2,0.2,0.4,0.4,0.5,0.7
                    s0.2,0.6,0.3,0.9s0.1,0.6,0.1,1s0,0.7-0.1,1L223.3,30.9z M222.3,28.8l-4.5,3.1c0,0.3,0,0.5,0.1,0.8c0.1,0.2,0.1,0.5,0.3,0.7
                    c0.1,0.2,0.3,0.3,0.5,0.5c0.2,0.1,0.5,0.2,0.8,0.2c0.4,0,0.8-0.1,1.1-0.2c0.3-0.2,0.5-0.4,0.7-0.6c0.2-0.3,0.4-0.6,0.5-0.9
                    c0.1-0.3,0.2-0.7,0.3-1.1L222.3,28.8z M218,30.6l4.6-3.2c0-0.3,0-0.5-0.1-0.8s-0.1-0.5-0.3-0.7c-0.1-0.2-0.3-0.4-0.5-0.5
                    c-0.2-0.1-0.5-0.2-0.8-0.2c-0.4,0-0.8,0.1-1.1,0.2c-0.3,0.2-0.5,0.4-0.7,0.6c-0.2,0.3-0.4,0.6-0.5,0.9c-0.1,0.3-0.2,0.7-0.3,1.1
                    L218,30.6z"></path>
        <path d="M280.3,35H279l1.5-8.9l-2.9,1.1l0.2-1.3l4.1-1.5h0.1L280.3,35z"></path>
        <path d="M342.5,35h-6.8l0.2-1l3.9-3.8c0.2-0.2,0.4-0.4,0.6-0.6c0.2-0.2,0.4-0.5,0.6-0.7s0.4-0.5,0.5-0.8
                    c0.1-0.3,0.2-0.6,0.3-0.9c0-0.2,0-0.5,0-0.7c-0.1-0.2-0.1-0.4-0.3-0.6s-0.3-0.3-0.5-0.4c-0.2-0.1-0.4-0.2-0.7-0.2
                    c-0.3,0-0.7,0-0.9,0.1s-0.5,0.3-0.7,0.4c-0.2,0.2-0.4,0.4-0.5,0.7c-0.1,0.3-0.2,0.6-0.2,0.9l-1.3,0c0-0.5,0.2-0.9,0.4-1.3
                    c0.2-0.4,0.5-0.8,0.8-1s0.7-0.5,1.1-0.7c0.4-0.2,0.9-0.2,1.4-0.2c0.4,0,0.8,0.1,1.2,0.2c0.4,0.1,0.7,0.3,0.9,0.6
                    c0.3,0.3,0.4,0.6,0.6,0.9c0.1,0.4,0.2,0.7,0.1,1.2c0,0.4-0.1,0.7-0.3,1.1c-0.2,0.3-0.4,0.7-0.6,1c-0.2,0.3-0.5,0.6-0.8,0.9
                    c-0.3,0.3-0.5,0.5-0.8,0.8l-3.3,3.1h5.1L342.5,35z"></path>

        <g transform="matrix(1,0,0,1,97.7877,-27.7644)">
          <text x="44.027px" y="102.839px" style="
            font-family: 'RobotoMono-Italic', 'Roboto Mono', monospace;
            font-style: italic;
            font-size: 15px;
            fill: rgb(221, 221, 221);
          ">
            Bit 1
          </text>
        </g>
        <g transform="matrix(1,0,0,1,97.7877,31.8425)">
          <text x="44.027px" y="102.839px" style="
            font-family: 'RobotoMono-Italic', 'Roboto Mono', monospace;
            font-style: italic;
            font-size: 15px;
            fill: rgb(221, 221, 221);
          ">
            Bit 2
          </text>
        </g>
      </g>
      <g class="beat doh">
        <rect x="260" y="50" rx="3" width="40" height="40"></rect>
      </g>
      <g class="beat ray">
        <g transform="matrix(1,0,0,2.50074,-60,-225.111)">
          <path d="M420,111.6C420,110.717 418.208,110 416,110L384,110C381.792,110 380,110.717 380,111.6L380,148.4C380,149.283 381.792,150 384,150L416,150C418.208,150 420,149.283 420,148.4L420,111.6Z"></path>
        </g>
      </g>
      <g class="beat egon">
        <rect x="380" y="110" rx="3" width="40" height="40"></rect>
        <rect x="380" y="50" rx="3" width="40" height="40"></rect>
      </g>
    </svg>

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

<div>
      <svg width="100%" height="100%" viewBox="0 0 1397 609" version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:serif="http://www.serif.com/" style="
            fill-rule: evenodd;
            clip-rule: evenodd;
            stroke-linejoin: round;
            stroke-miterlimit: 1.5;
          ">
        <g id="OR" transform="matrix(4.16667,0,0,4.16667,-391.071,-708.313)">
          <g transform="matrix(0.24,0,0,0.24,1.80858,1.65656)">
            <text id="ORinput1" x="464.17px" y="1027.95px" style="font-family: RobotoMono-Bold, &quot;Roboto Mono&quot;, monospace; font-size: 75px; fill: rgb(182, 190, 201); cursor: pointer;">0</text>
          </g>
          <g transform="matrix(0.24,0,0,0.24,1.80858,49.9972)">
            <text id="ORinput2" x="464.17px" y="1027.95px" style="font-family: RobotoMono-Bold, &quot;Roboto Mono&quot;, monospace; font-size: 75px; fill: rgb(182, 190, 201); cursor: pointer;">0</text>
          </g>
          <g id="ORBild1" transform="matrix(0.0736747,0,0,0.0736747,388.573,246.182)" style="display: none;">
            <use xlink:href="#_Image1" x="0" y="0" width="550px" height="546px"></use>
          </g>
          <g id="ORBild0" transform="matrix(0.0675142,0,0,0.0675142,388.667,246.449)" style="display: block;">
            <use xlink:href="#_Image2" x="0" y="0" width="596px" height="588px"></use>
          </g>
          <g id="Rahmen">
            <g transform="matrix(0.24,0,0,0.24,1.15205,2.01719)">
              <text x="878.632px" y="815.38px" style="
                    font-family: 'Roboto-Regular', 'Roboto';
                    font-size: 45.833px;
                    fill: rgb(182, 190, 201);
                  ">
                1
              </text>
            </g>
            <g transform="matrix(0.24,0,0,0.24,49.3337,1.81585)">
              <text x="878.632px" y="815.38px" style="
                    font-family: 'Roboto-Regular', 'Roboto';
                    font-size: 45.833px;
                    fill: rgb(182, 190, 201);
                  ">
                2
              </text>
            </g>
            <g transform="matrix(0.24,0,0,0.24,-47.5274,50.3523)">
              <text x="878.632px" y="815.38px" style="
                    font-family: 'Roboto-Regular', 'Roboto';
                    font-size: 45.833px;
                    fill: rgb(182, 190, 201);
                  ">
                Nr. 1
              </text>
            </g>
            <g transform="matrix(0.24,0,0,0.24,-47.7236,98.8499)">
              <text x="878.632px" y="815.38px" style="
                    font-family: 'Roboto-Regular', 'Roboto';
                    font-size: 45.833px;
                    fill: rgb(182, 190, 201);
                  ">
                Nr. 2
              </text>
            </g>
            <g transform="matrix(0.24,0,0,0.24,97.8913,1.96746)">
              <text x="878.632px" y="815.38px" style="
                    font-family: 'Roboto-Regular', 'Roboto';
                    font-size: 45.833px;
                    fill: rgb(182, 190, 201);
                  ">
                3
              </text>
            </g>
            <g transform="matrix(0.24,0,0,0.24,0,0)">
              <path d="M585.242,806.117L601.208,822.082L601.208,813.269L601.208,822.082L591.881,822.082" style="
                    fill: none;
                    stroke: rgb(182, 190, 201);
                    stroke-width: 3.33px;
                  "></path>
            </g>
            <g transform="matrix(0.24,0,0,0.24,0,0)">
              <path d="M794.464,709.98L794.464,1314.89" style="
                    fill: none;
                    stroke: rgb(244, 247, 249);
                    stroke-width: 3.33px;
                  "></path>
            </g>
            <g transform="matrix(2.93869e-17,-0.479924,0.479924,2.93869e-17,-246.479,599.671)">
              <path d="M794.464,709.98L794.464,1314.89" style="
                    fill: none;
                    stroke: rgb(244, 247, 249);
                    stroke-width: 1.67px;
                  "></path>
            </g>
            <g transform="matrix(0.24,0,0,0.24,0,0)">
              <path d="M796.131,1014.18L1308.01,1014.18" style="
                    fill: none;
                    stroke: rgb(107, 128, 147);
                    stroke-width: 3.33px;
                  "></path>
            </g>
            <g transform="matrix(0.24,0,0,0.24,0,48.4575)">
              <path d="M796.131,1014.18L1308.01,1014.18" style="
                    fill: none;
                    stroke: rgb(107, 128, 147);
                    stroke-width: 3.33px;
                  "></path>
            </g>
            <g transform="matrix(0.161433,0,0,0.161433,173.437,103.862)">
              <path d="M796.131,1014.18L1308.01,1014.18" style="
                    fill: none;
                    stroke: rgb(107, 128, 147);
                    stroke-width: 4.96px;
                  "></path>
            </g>
            <g transform="matrix(0.24,0,0,0.24,146.189,2.00236)">
              <text x="878.632px" y="815.38px" style="
                    font-family: 'Roboto-Regular', 'Roboto';
                    font-size: 45.833px;
                    fill: rgb(182, 190, 201);
                  ">
                4
              </text>
            </g>
            <g transform="matrix(0.24,0,0,0.561566,0,-301.026)">
              <path d="M1375.81,939.607C1375.81,937.817 1372.4,936.363 1368.21,936.363L1231.57,936.363C1227.38,936.363 1223.98,937.817 1223.98,939.607L1223.98,1084.94C1223.98,1086.74 1227.38,1088.19 1231.57,1088.19L1368.21,1088.19C1372.4,1088.19 1375.81,1086.74 1375.81,1084.94L1375.81,939.607Z" style="fill: rgb(80, 90, 99)"></path>
            </g>
            <g transform="matrix(0.24,0,0,0.24,0.87781,6.54295)">
              <text x="1265.97px" y="1119px" style="
                    font-family: 'Roboto-Regular', 'Roboto';
                    font-size: 83.333px;
                    fill: white;
                  ">
                ≥1
              </text>
            </g>
          </g>
        </g>
      </svg>
      <button class="Q-button curriculum-classic-color" id="evaluate-button" type="button" onclick="auswertenOR(); this.blur();"><font dir="auto" style="vertical-align: inherit;"><font dir="auto" style="vertical-align: inherit;">
        Đo lường
      </font></font></button>
    </div>


**Kết quả cho ra là 0




<div>
      <svg width="100%" height="100%" viewBox="0 0 1397 609" version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:serif="http://www.serif.com/" style="
            fill-rule: evenodd;
            clip-rule: evenodd;
            stroke-linejoin: round;
            stroke-miterlimit: 1.5;
          ">
        <g id="OR" transform="matrix(4.16667,0,0,4.16667,-391.071,-708.313)">
          <g transform="matrix(0.24,0,0,0.24,1.80858,1.65656)">
            <text id="ORinput1" x="464.17px" y="1027.95px" style="font-family: RobotoMono-Bold, &quot;Roboto Mono&quot;, monospace; font-size: 75px; fill: rgb(182, 190, 201); cursor: pointer;">1</text>
          </g>
          <g transform="matrix(0.24,0,0,0.24,1.80858,49.9972)">
            <text id="ORinput2" x="464.17px" y="1027.95px" style="font-family: RobotoMono-Bold, &quot;Roboto Mono&quot;, monospace; font-size: 75px; fill: rgb(182, 190, 201); cursor: pointer;">0</text>
          </g>
          <g id="ORBild1" transform="matrix(0.0736747,0,0,0.0736747,388.573,246.182)" style="display: block;">
            <use xlink:href="#_Image1" x="0" y="0" width="550px" height="546px"></use>
          </g>
          <g id="ORBild0" transform="matrix(0.0675142,0,0,0.0675142,388.667,246.449)" style="display: none;">
            <use xlink:href="#_Image2" x="0" y="0" width="596px" height="588px"></use>
          </g>
          <g id="Rahmen">
            <g transform="matrix(0.24,0,0,0.24,1.15205,2.01719)">
              <text x="878.632px" y="815.38px" style="
                    font-family: 'Roboto-Regular', 'Roboto';
                    font-size: 45.833px;
                    fill: rgb(182, 190, 201);
                  ">
                1
              </text>
            </g>
            <g transform="matrix(0.24,0,0,0.24,49.3337,1.81585)">
              <text x="878.632px" y="815.38px" style="
                    font-family: 'Roboto-Regular', 'Roboto';
                    font-size: 45.833px;
                    fill: rgb(182, 190, 201);
                  ">
                2
              </text>
            </g>
            <g transform="matrix(0.24,0,0,0.24,-47.5274,50.3523)">
              <text x="878.632px" y="815.38px" style="
                    font-family: 'Roboto-Regular', 'Roboto';
                    font-size: 45.833px;
                    fill: rgb(182, 190, 201);
                  ">
                Nr. 1
              </text>
            </g>
            <g transform="matrix(0.24,0,0,0.24,-47.7236,98.8499)">
              <text x="878.632px" y="815.38px" style="
                    font-family: 'Roboto-Regular', 'Roboto';
                    font-size: 45.833px;
                    fill: rgb(182, 190, 201);
                  ">
                Nr. 2
              </text>
            </g>
            <g transform="matrix(0.24,0,0,0.24,97.8913,1.96746)">
              <text x="878.632px" y="815.38px" style="
                    font-family: 'Roboto-Regular', 'Roboto';
                    font-size: 45.833px;
                    fill: rgb(182, 190, 201);
                  ">
                3
              </text>
            </g>
            <g transform="matrix(0.24,0,0,0.24,0,0)">
              <path d="M585.242,806.117L601.208,822.082L601.208,813.269L601.208,822.082L591.881,822.082" style="
                    fill: none;
                    stroke: rgb(182, 190, 201);
                    stroke-width: 3.33px;
                  "></path>
            </g>
            <g transform="matrix(0.24,0,0,0.24,0,0)">
              <path d="M794.464,709.98L794.464,1314.89" style="
                    fill: none;
                    stroke: rgb(244, 247, 249);
                    stroke-width: 3.33px;
                  "></path>
            </g>
            <g transform="matrix(2.93869e-17,-0.479924,0.479924,2.93869e-17,-246.479,599.671)">
              <path d="M794.464,709.98L794.464,1314.89" style="
                    fill: none;
                    stroke: rgb(244, 247, 249);
                    stroke-width: 1.67px;
                  "></path>
            </g>
            <g transform="matrix(0.24,0,0,0.24,0,0)">
              <path d="M796.131,1014.18L1308.01,1014.18" style="
                    fill: none;
                    stroke: rgb(107, 128, 147);
                    stroke-width: 3.33px;
                  "></path>
            </g>
            <g transform="matrix(0.24,0,0,0.24,0,48.4575)">
              <path d="M796.131,1014.18L1308.01,1014.18" style="
                    fill: none;
                    stroke: rgb(107, 128, 147);
                    stroke-width: 3.33px;
                  "></path>
            </g>
            <g transform="matrix(0.161433,0,0,0.161433,173.437,103.862)">
              <path d="M796.131,1014.18L1308.01,1014.18" style="
                    fill: none;
                    stroke: rgb(107, 128, 147);
                    stroke-width: 4.96px;
                  "></path>
            </g>
            <g transform="matrix(0.24,0,0,0.24,146.189,2.00236)">
              <text x="878.632px" y="815.38px" style="
                    font-family: 'Roboto-Regular', 'Roboto';
                    font-size: 45.833px;
                    fill: rgb(182, 190, 201);
                  ">
                4
              </text>
            </g>
            <g transform="matrix(0.24,0,0,0.561566,0,-301.026)">
              <path d="M1375.81,939.607C1375.81,937.817 1372.4,936.363 1368.21,936.363L1231.57,936.363C1227.38,936.363 1223.98,937.817 1223.98,939.607L1223.98,1084.94C1223.98,1086.74 1227.38,1088.19 1231.57,1088.19L1368.21,1088.19C1372.4,1088.19 1375.81,1086.74 1375.81,1084.94L1375.81,939.607Z" style="fill: rgb(80, 90, 99)"></path>
            </g>
            <g transform="matrix(0.24,0,0,0.24,0.87781,6.54295)">
              <text x="1265.97px" y="1119px" style="
                    font-family: 'Roboto-Regular', 'Roboto';
                    font-size: 83.333px;
                    fill: white;
                  ">
                ≥1
              </text>
            </g>
          </g>
        </g>
      </svg>
      <button class="Q-button curriculum-classic-color" id="evaluate-button" type="button" onclick="auswertenOR(); this.blur();"><font dir="auto" style="vertical-align: inherit;"><font dir="auto" style="vertical-align: inherit;">
        Đo lường
      </font></font></button>
    </div>

**Kết quả cho ra là 1

! Ta thấy rảng qua cổng >=1 ít nhất 1 bit có giá trị lớn hơn hoặc bằng 1 thì sẽ cho ra kết quả là 1 còn lại sẽ cho kết quả bằng 0

## Cổng NAND
Giỏi lắm! Nó sẽ biến đầu ra thành 1 nếu ít nhất một đầu vào là 0. Cổng này thực chất được gọi là cổng NAND (Not AND). Đây là cổng duy nhất cần thiết để thực hiện mọi phép tính mà một máy tính truyền thống có thể làm được.

# Từ Điện toán hiện đại đến điện toán lượng tử

Trong khi máy tính trên bàn làm việc của chúng ta tuân theo các định luật **vật lý cổ điển** và do đó đôi khi cũng được gọi là **máy tính cổ điển**, thì máy tính lượng tử tuân theo các quy tắc hoàn toàn khác - các quy luật của vật lý lượng tử. 
Vật lý lượng tử bắt nguồn từ những khám phá khoa học cho thyaas các nguyên tử và electron hoạt động hoàn toàn khác so với vật thể ở kích thước con người. Nhưng chúng ta sẽ tập trung không nhiều vào khía cạnh vật lý, mà vào cách hành vi này có thể được sử dụng trong điện toán lượng tử.
# Qbit 

> Thay vì là bit, đơn vị nhỏ nhất của thông tin lượng tử là bit lượng tử hay qubit. Chúng tượng tự như bit ở chỗ có hai trạng thái đo được, thường được gọi bằng $|0\rangle$ và $|1\rangle$. Tuy nhiên, cũng có những điểm khác biệt đáng kể, mà chúng ta sẽ tìm hiểu ở phần tiếp theo.

Để đạt được mục tiêu này, chúng ta sẽ khám phá cách thao tác các qubit bằng máy tính lượng tử. Cũng như máy tính cổ điển, chúng ta có thể mô tả máy tính lượng tử bằng các cổng lượng tử này, chúng ta sẽ hiểu rõ hơn về các qubit và cách thức hoạt động của máy tính lượng tử.
Hiện tại chúng ta chưa có sự hiểu biết trực quan về qubit, nhưng chúng ta sẽ xây dựng được một qubit trong quá trình này!

## X cổng 
> !

Khám phá những tác động của Xcổng trên qubit bằng cách  
• thay đổi giá trị đầu vào từ $|0\rangle$ ĐẾN $|1\rangle$   
• và thêm một cái thứ hai X-cổng  
và thực hiện một phép đo mỗi lần.

![assets/gif/x-gate.gif](https://www.iqmacademy.com/curriculum/assets/gif/x-gate.gif)    

- Mỗi dòng đại diện cho một qubit. 
- Các vị trí khác nhau (1, 2, 3, 4) mà cổng có thể được đặt biểu thị các bước thời gian khác nhau mà máy tính lượng tử có thể thực hiện một phép toán.

XCổng này hoạt động rất giống với cổng NOT trong máy tính cổ điển. Thậm chí đôi khi nó còn được gọi là cổng NOT lượng tử.

