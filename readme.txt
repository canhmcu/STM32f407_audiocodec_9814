Ý TƯỞNG

- có nhiều loại board phát triển với dòng stm32

STM32F0 Discovery:

Chức năng: Dành cho dòng STM32F0, nó là một board đánh giá giúp bạn làm quen với các tính năng của dòng này.
STM32F4 Discovery:

Chức năng: Dành cho dòng STM32F4, board này thường được sử dụng để phát triển ứng dụng đòi hỏi hiệu suất cao và tích hợp nhiều tính năng.
STM32F7 Discovery:

Chức năng: Dành cho dòng STM32F7, board này cung cấp hiệu suất cao và tích hợp màn hình cảm ứng.
STM32L4 Discovery:

Chức năng: Dành cho dòng STM32L4, board này tập trung vào tiết kiệm năng lượng và phù hợp cho các ứng dụng di động.
STM32 Nucleo Boards:

Chức năng: Dòng Nucleo bao gồm nhiều loại board dành cho nhiều dòng STM32 khác nhau. Chúng thường được thiết kế để linh hoạt và tương thích với nhiều shields mở rộng khác nhau.
STM32H7 Discovery:

Chức năng: Dành cho dòng STM32H7, cung cấp hiệu suất cao với lõi Cortex-M7 và tích hợp nhiều tính năng.
STM32WB Nucleo:

Chức năng: Dành cho dòng STM32WB, tích hợp Bluetooth và Wi-Fi, thích hợp cho ứng dụng IoT không dây.
STM32MP1 Discovery:

Chức năng: Dành cho dòng STM32MP1, tích hợp cả lõi Cortex-A và Cortex-M, thích hợp cho các ứng dụng đòi hỏi sự kết hợp của Linux và thời gian thực.
STM32 IoT Discovery Kit:

Chức năng: Được thiết kế đặc biệt cho ứng dụng IoT, tích hợp nhiều cảm biến và tính năng kết nối.

- dự án này mục đích tạo ra nhằm xử lý tín hiệu âm thanh, thiết bị thu âm thanh từ mic sau đó truyền đi đến nơi mong muốn
- với board mạch nghiên cứu là mạch stm32f407vgt6 discovery, ban đầu tôi định thu âm thanh bằng MEMs MP45DT02 sau đó nó xuất ra output là mã PDM, mã này đi vào vdk được chuyển đổi thành mã PCM, mã PCM đi vào audio codec là CS43L22, con codec này sẽ nhận mã và phát ra âm  thanh, mình nối tai nghe vào jack cắm là nghe được.
- tuy nhiên quá trình làm thì gặp vấn đề là ko nghe được :))) tôi quyết định sử dụng con max9814 thay cho con MEMs, con này sẽ thu âm và xuất ra đầu ra analog đi vào vdk, sau đó vdk thực hiện ADC giá trị này và đưa giá trị vừa được ADC vào con CS43L22 để con này phát ra âm thanh.

TÀI LIỆU LIÊN QUAN

- đọc kỹ datasheet con CS43L22 trong project, con này có 2 loại đầu vào là đầu vào I2C và đầu vào I2S, cả 2 kiểu đầu vào đều được làm ví dụ trong video dưới đây, bên cạnh đó video này còn hướng dẫn bạn thêm thư viện điều khiển con CS43L22 nữa "https://www.youtube.com/watch?v=QIPQOnVablY&list=PLfExI9i0v1sn_lQjCFJHrDSpvZ8F2CpkA&index=30"
- chú ý cấu hình chân giống như video

CODE

- tiến hành cấu hình chân, chú ý cấu hình chân ADC, chọn độ phân giải 12bit, ADC1 thì sử dụng TIMER2, TIMER2 thì dùng clock của APB1, cấu hình tần số lấy mẫu là 48k, đây là tần số lấy mẫu phù hợp với âm thanh thông thường, cấu hình i2c, i2s theo video
- tiến hành thêm thư viện
- trong code thì define DATASIZE là 2048, vì độ phân giải ADC là 12bit tương ứng với 4096 giá trị, ta đặt DATASIZE là 2048 để khi truyền ta chia ra 2 lần truyền, đó là khi dữ liệu nhận được 1 nửa và khi dữ liệu nhận được hết.
- tiếp đó ta khởi tạo mảng in chứa dữ liệu ADC, mảng out là mảng in nhân đôi lên để đưa ra con CS43L22 bằng giao thức i2s, việc nhân đôi dữ liệu là do âm thanh chia ra 2 đường đi đến tai nghe trái và tai nghe phải
- bắt đầu từ dòng 425 ta thêm các hàm để set cờ thông báo nhận được 1 nửa dữ liệu và nhận hết dữ liệu, sau đó vào main thì dựa vào trạng thái các cờ để đưa in vào out, ở trước while 1 thì ta đã gọi hàm truyền DMA_I2S cho CS43L22 để nó phát âm thanh. 
