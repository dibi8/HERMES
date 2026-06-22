---
title: "Ultralytics YOLO: Hướng dẫn toàn diện về Phát hiện đối tượng và Phân đoạn ảnh trong năm 2026"
slug: ultralytics-yolo-guide
date: 2026-01-15
tags:
  - computer-vision
  - deep-learning
  - object-detection
  - segmentation
  - open-source
  - ultralytics
  - yolo
categories:
  - ai-tools
  - tutorials
description: "Đánh giá và hướng dẫn toàn diện về Ultralytics YOLO, bao gồm cài đặt, sử dụng nâng cao, benchmark và triển khai sản xuất cho năm 2026."
image: https://raw.githubusercontent.com/ultralytics/assets/main/yolov8/banner.png
author: Agnes-2.0-Flash
---

# Ultralytics YOLO: Hướng dẫn toàn diện về Phát hiện đối tượng và Phân đoạn ảnh trong năm 2026 — Đánh giá công cụ AI mã nguồn mở

Trong bối cảnh thị giác máy tính (computer vision) phát triển nhanh chóng, ít có công cụ nào khẳng định vị trí là cơ sở hạ tầng cốt lõi một cách nhất quán như Ultralytics YOLO. Khi chúng ta bước vào năm 2026, nhu cầu về phát hiện đối tượng và phân đoạn theo thời gian thực với độ chính xác cao chưa từng tăng mạnh, trải rộng từ các ngành như lái xe tự hành đến nông nghiệp chính xác. Bài viết này cung cấp phân tích chuyên sâu về thư viện Ultralytics, khám phá kiến trúc, quy trình cài đặt và các ứng dụng thực tế dành cho nhà phát triển và nhà khoa học dữ liệu. Bằng cách xem xét các chỉ số hiệu suất, khả năng tích hợp và chiến lược triển khai, chúng tôi nhằm mục đích trang bị cho bạn kiến thức cần thiết để triển khai các mô hình thị giác mạnh mẽ trong dự án của mình. Dù bạn là người mới bắt đầu muốn hiểu những kiến thức cơ bản hay chuyên gia tìm kiếm kỹ thuật tối ưu hóa, hướng dẫn này cung cấp một lộ trình có cấu trúc để làm chủ một trong những công cụ AI mã nguồn mở phổ biến nhất hiện nay.

![Ultralytics YOLO Banner](https://raw.githubusercontent.com/ultralytics/assets/main/yolov8/banner.png)

## Ultralytics YOLO là gì?

Ultralytics là một gói Python cung cấp giao diện thống nhất để huấn luyện và triển khai các mô hình học máy tiên tiến nhất, chủ yếu tập trung vào họ thuật toán You Only Look Once (YOLO). Thư viện hỗ trợ ba nhiệm vụ chính: phát hiện đối tượng, phân đoạn thể hiện (instance segmentation) và phân loại ảnh. Không giống như nhiều khung làm việc khác yêu cầu cấu hình phức tạp cho các kiến trúc mô hình khác nhau, Ultralytics trừu tượng hóa phần lớn sự phức tạp này, cho phép người dùng huấn luyện, xác thực, dự đoán, xuất và theo dõi đối tượng với lượng mã tối thiểu.

Dự án được duy trì bởi Glenn Jocher và cộng đồng rộng lớn, đã đạt được sức hút đáng kể nhờ tính dễ sử dụng và hiệu suất cao. Vào năm 2026, kho lưu trữ tiếp tục là một trong những dự án được sao chép (starred) nhiều nhất trên GitHub, phản ánh mức độ áp dụng rộng rãi trong cả nghiên cứu học thuật và ứng dụng công nghiệp. Triết lý cốt lõi đằng sau Ultralytics là sự đơn giản và tốc độ, cho phép các nhà phát triển lặp lại nhanh chóng trên các đường ống thị giác máy tính của họ.

Các tính năng chính bao gồm hỗ trợ nhiều trọng lượng đã huấn luyện trước (như YOLOv8, YOLOv9 và YOLOv10), tinh chỉnh siêu tham số tự động và tích hợp liền mạch với các công cụ trực quan hóa dữ liệu phổ biến. Thư viện cũng nhấn mạnh tính mô-đun, cho phép người dùng dễ dàng thay thế các mạng backbone hoặc các đầu ra (heads) trong đường ống phát hiện mà không cần viết lại toàn bộ kịch bản. Sự linh hoạt này khiến nó trở thành lựa chọn lý tưởng cho các đội ngũ muốn thử nghiệm nhanh trong khi vẫn duy trì tùy chọn mở rộng quy mô cho môi trường sản xuất.

## Ultralytics YOLO hoạt động như thế nào

Hiểu các cơ chế nền tảng của Ultralytics YOLO đòi hỏi phải xem xét kiến trúc mô-đun của nó. Khung làm việc được xây dựng dựa trên PyTorch, tận dụng các khả năng đồ thị tính toán động của nó để tạo điều kiện thuận lợi cho việc thử nghiệm và gỡ lỗi dễ dàng. Về cốt lõi, thư viện sử dụng phương pháp điều khiển bằng cấu hình (config-driven), nơi các định nghĩa mô hình được lưu trữ trong các tệp YAML. Các tệp cấu hình này chỉ định cấu trúc mạng, bao gồm các lớp, kênh và hàm kích hoạt, cho phép sửa đổi nhanh chóng thiết kế mô hình.

Khi huấn luyện một mô hình, Ultralytics xử lý toàn bộ đường ống một cách tự động. Điều này bao gồm tải dữ liệu, tăng cường dữ liệu, tính toán tổn thất, lan truyền ngược gradient và lưu điểm kiểm tra (checkpoint). Thư viện sử dụng một API thống nhất cho tất cả các tác vụ, có nghĩa là dù bạn đang thực hiện phân loại hay phân đoạn, giao diện dòng lệnh (CLI) vẫn giữ nguyên tương tự. Sự nhất quán này giảm đáng kể đường cong học tập so với các khung làm việc khác có thể có các API riêng biệt cho các tác vụ khác nhau.

Đối với suy luận (inference), khung làm việc tối ưu hóa các mô hình cho nhiều bộ tăng tốc phần cứng khác nhau. Nó hỗ trợ xuất mô hình sang các định dạng như ONNX, TensorRT, CoreML và TFLite, đảm bảo tương thích trên nhiều mục tiêu triển khai khác nhau. Chức năng theo dõi, vốn rất quan trọng cho các kịch bản theo dõi đa đối tượng, được tích hợp trực tiếp vào vòng lặp dự đoán, cho phép nhận dạng và liên kết đối tượng theo thời gian thực qua các khung hình video. Ngoài ra, thư viện bao gồm các tiện ích để chuyển đổi chú thích tập dữ liệu, hỗ trợ các định dạng phổ biến như COCO, VOC và YOLO, giúp đơn giản hóa giai đoạn chuẩn bị của bất kỳ dự án thị giác máy tính nào.

## Cài đặt & Thiết lập

Bắt đầu với Ultralytics YOLO rất đơn giản. Phương pháp chính là cài đặt gói `ultralytics` thông qua pip. Bạn nên tạo một môi trường ảo để quản lý các phụ thuộc một cách hiệu quả. Dưới đây là các bước để cài đặt thư viện và xác minh thiết lập.

Trước tiên, hãy đảm bảo bạn đã cài đặt Python (phiên bản 3.8 trở lên). Sau đó, tạo và kích hoạt một môi trường ảo:

```bash
python -m venv yolo-env
source yolo-env/bin/activate  # Trên Windows: yolo-env\Scripts\activate
```

Tiếp theo, cài đặt gói Ultralytics cùng với các phụ thuộc tùy chọn để tăng cường chức năng:

```bash
pip install ultralytics[export]
```

Để xác minh cài đặt, bạn có thể chạy một kịch bản kiểm tra đơn giản để kiểm tra phiên bản và chức năng cơ bản:

```python
from ultralytics import YOLO

# Kiểm tra phiên bản
print(YOLO.__version__)

# Tải mô hình đã huấn luyện trước
model = YOLO('yolov8n.pt')

# Chạy suy luận trên một ảnh mẫu
results = model.predict(source='sample_image.jpg', save=True)
```

Nếu bạn muốn sao chép kho lưu trữ trực tiếp cho mục đích phát triển, bạn có thể sử dụng Git:

```bash
git clone https://github.com/ultralytics/ultralytics.git
cd ultralytics
pip install -e .
```

Đối với người dùng yêu cầu các phiên bản CUDA cụ thể hoặc làm việc trong các môi trường hạn chế, các hình ảnh Docker cũng được cung cấp bởi cộng đồng và các kênh chính thức. Sử dụng Docker đảm bảo một môi trường nhất quán trên các máy khác nhau:

```bash
docker pull ultralytics/ultralytics:latest
docker run -it --gpus all -v ./data:/app/data ultralytics/ultralytics:latest
```

## Tích hợp với các công cụ phổ biến

Ultralytics YOLO tích hợp liền mạch với nhiều công cụ phổ biến trong hệ sinh thái khoa học dữ liệu. Một trong những tích hợp đáng chú ý nhất là với Weights & Biases (W&B) để theo dõi và trực quan hóa thí nghiệm. Điều này cho phép các nhà phát triển giám sát các chỉ số huấn luyện, trực quan hóa các dự đoán và so sánh các lần chạy mô hình khác nhau theo thời gian thực.

Để bật tích hợp W&B, bạn cần đặt đối số `project` trong quá trình huấn luyện:

```python
from ultralytics import YOLO

model = YOLO('yolov8n.yaml')
model.train(
    data='coco128.yaml',
    epochs=100,
    imgsz=640,
    project='my_yolo_project',
    name='run1'
)
```

Một tích hợp mạnh mẽ khác là với Gradio, giúp tạo ra các giao diện web tương tác cho các mô hình của bạn. Điều này đặc biệt hữu ích để trình diễn khả năng cho các bên liên quan hoặc xây dựng các ứng dụng frontend đơn giản.

Dưới đây là ví dụ về việc tạo một giao diện Gradio đơn giản cho phát hiện đối tượng:

```python
import gradio as gr
from ultralytics import YOLO

# Tải mô hình
model = YOLO('yolov8n.pt')

def detect(image):
    results = model.predict(image, save=True, verbose=False)
    return results[0].plot()

# Tạo ứng dụng Gradio
demo = gr.Interface(
    fn=detect,
    inputs=gr.Image(type="pil"),
    outputs=gr.Image(),
    title="Ultralytics YOLO Demo",
    description="Tải ảnh lên để phát hiện đối tượng"
)

if __name__ == "__main__":
    demo.launch()
```

Ultralytics cũng hỗ trợ tích hợp với Hugging Face Datasets, giúp dễ dàng hơn trong việc tải và tiền xử lý các tập dữ liệu quy mô lớn. Tích hợp này cho phép người dùng sử dụng bộ sưu tập rộng lớn các tập dữ liệu có sẵn trên Hugging Face Hub trực tiếp trong các đường ống huấn luyện của họ.

```python
from datasets import load_dataset
from ultralytics import YOLO

# Tải tập dữ liệu từ Hugging Face
dataset = load_dataset('coco', split='train[:1000]')

# Chuyển đổi sang định dạng YOLO nếu cần (tự động trong một số trường hợp)
# Huấn luyện mô hình
model = YOLO('yolov8n.pt')
model.train(data=dataset, epochs=5)
```

## Các chỉ số Benchmark (Đánh giá hiệu năng)

Đánh giá hiệu suất là cực kỳ quan trọng khi chọn một công cụ thị giác máy tính. Ultralytics YOLO luôn xếp hạng cao trong các bài kiểm tra benchmark về tốc độ và độ chính xác. Thư viện cung cấp các tiện ích tích hợp để đánh giá các mô hình trên các tập dữ liệu tiêu chuẩn như COCO và Pascal VOC.

Để benchmark một mô hình đã huấn luyện trước trên tập xác thực COCO, bạn có thể sử dụng lệnh sau:

```bash
yolo val model=yolov8n.pt data=coco.yaml
```

Để có cách tiếp cận lập trình chi tiết hơn, bạn có thể tính toán các chỉ số như mAP (mean Average Precision), độ chính xác (precision), độ thu hồi (recall) và điểm F1:

```python
from ultralytics import YOLO

model = YOLO('yolov8n.pt')
metrics = model.val(data='coco.yaml')
print(f"mAP@0.5: {metrics.box.map50}")
print(f"mAP@0.5:0.95: {metrics.box.map}")
print(f"Precision: {metrics.box.mp}")
print(f"Recall: {metrics.box.mr}")
```

Dưới đây là bảng so sánh cho thấy các chỉ số hiệu suất điển hình cho các biến thể YOLO khác nhau trên tập dữ liệu COCO. Lưu ý rằng các số liệu này mang tính minh họa và có thể thay đổi tùy theo phần cứng và các điều kiện thí nghiệm cụ thể.

| Biến thể Mô hình | Tham số (M) | GFLOPs | mAPval 0.5:0.95 | Tốc độ (ms) |
|---------------|----------------|--------|-----------------|------------|
| YOLOv8n       | 3.2            | 8.7    | 37.3            | 1.47       |
| YOLOv8s       | 11.2           | 28.6   | 44.9            | 2.65       |
| YOLOv8m       | 25.9           | 78.9   | 50.2            | 5.61       |
| YOLOv8l       | 43.7           | 165.2  | 52.9            | 9.06       |
| YOLOv8x       | 68.2           | 257.8  | 53.9            | 13.3       |

*Bảng 1: So sánh hiệu suất của các biến thể YOLOv8 trên tập dữ liệu COCO.*

Các benchmark này làm nổi bật sự đánh đổi giữa kích thước mô hình, chi phí tính toán và độ chính xác. Đối với các ứng dụng thời gian thực trên các thiết bị biên (edge devices), các mô hình nhỏ hơn như YOLOv8n hoặc YOLOv8s được ưa chuộng. Đối với các yêu cầu độ chính xác cao nơi độ trễ không quá quan trọng, các mô hình lớn hơn như YOLOv8l hoặc YOLOv8x mang lại hiệu suất vượt trội.

## Sử dụng nâng cao: Triển khai sản xuất

Triển khai các mô hình đã huấn luyện vào môi trường sản xuất đòi hỏi sự cân nhắc cẩn thận về độ trễ, thông lượng và các ràng buộc về tài nguyên. Ultralytics YOLO hỗ trợ xuất các mô hình sang nhiều định dạng được tối ưu hóa. Một trong những định dạng hiệu quả nhất cho suy luận hiệu suất cao là TensorRT, đặc biệt là cho các GPU NVIDIA.

Để xuất một mô hình sang định dạng TensorRT, hãy sử dụng lệnh sau:

```bash
yolo export model=yolov8n.pt format=engine half=True device=0
```

Lệnh này tạo ra một tệp `.engine` có thể được tải và thực thi bằng cách sử dụng runtime TensorRT. Đối số `half=True` bật độ chính xác FP16, có thể tăng tốc đáng kể quá trình suy luận trên phần cứng được hỗ trợ.

Đối với các triển khai dựa trên CPU hoặc tương thích đa nền tảng, định dạng ONNX được hỗ trợ rộng rãi. Việc xuất sang ONNX rất đơn giản:

```bash
yolo export model=yolov8n.pt format=onnx simplify=True opset=12
```

Sau khi xuất, bạn có thể tải và chạy suy luận bằng cách sử dụng ONNX Runtime:

```python
import onnxruntime as ort
import numpy as np

# Tải mô hình ONNX
session = ort.InferenceSession("yolov8n.onnx")

# Chuẩn bị ảnh đầu vào
input_image = ... # Tiền xử lý ảnh thành 640x640 và chuẩn hóa
input_tensor = np.array([input_image]).astype(np.float32)

# Chạy suy luận
outputs = session.run(None, {session.get_inputs()[0].name: input_tensor})

# Hậu xử lý đầu ra để lấy các hộp giới hạn và lớp
boxes, scores, labels = post_process(outputs)
```

Đối với các thiết bị di động và thiết bị biên, CoreML (iOS) và TFLite (Android/ARM) là những lựa chọn phổ biến. Việc xuất sang CoreML có thể được thực hiện như sau:

```bash
yolo export model=yolov8n.pt format=coreml
```

Và đối với TFLite:

```bash
yolo export model=yolov8n.pt format=tflite
```

Các mô hình đã xuất này có thể được tích hợp vào các ứng dụng native iOS, Android hoặc Linux nhúng, đảm bảo thực thi hiệu quả với mức tiêu thụ pin tối thiểu và độ trễ thấp.

## So sánh với các giải pháp thay thế

Mặc dù Ultralytics YOLO là một lựa chọn hàng đầu, nhưng vẫn có các khung làm việc và thư viện khác trong lĩnh vực thị giác máy tính. Hiểu cách nó so sánh với các giải pháp thay thế có thể giúp đưa ra quyết định sáng suốt. Dưới đây là so sánh với một số công cụ phổ biến.

| Tính năng | Ultralytics YOLO | Detectron2 (Meta) | MMDetection (OpenMMLab) | TensorFlow Object Detection API |
|---------|------------------|-------------------|-------------------------|--------------------------------|
| Dễ sử dụng | Cao | Trung bình | Trung bình | Thấp |
| Tài liệu | Xuất sắc | Tốt | Tốt | Khá |
| Hỗ trợ cộng đồng | Rất lớn | Lớn | Lớn | Rất lớn |
| Tùy chọn triển khai | Mở rộng (ONNX, TensorRT, v.v.) | Hạn chế | Trung bình | Trung bình |
| Linh hoạt | Cao | Cao | Cao | Thấp |
| Khung làm việc chính | PyTorch | PyTorch | PyTorch | TensorFlow |
| Bảo trì tích cực | Rất tích cực | Tích cực | Tích cực | Được bảo trì nhưng cập nhật ít thường xuyên hơn |

*Bảng 2: So sánh Ultralytics YOLO với các khung làm việc CV phổ biến khác.*

Ultralytics YOLO nổi bật nhờ tính dễ sử dụng và tài liệu toàn diện. Trong khi Detectron2 và MMDetection cung cấp sự linh hoạt lớn hơn cho các kiến trúc mô hình tùy chỉnh, chúng đi kèm với đường cong học tập dốc hơn. API của TensorFlow rất mạnh mẽ nhưng thường yêu cầu nhiều mã khuôn mẫu hơn cho các tác vụ tương tự. Đối với hầu hết người dùng tìm kiếm sự cân bằng giữa hiệu suất,Ease of implementation (dễ triển khai) và linh hoạt trong triển khai, Ultralytics YOLO vẫn là một khuyến nghị hàng đầu.

## Hạn chế

Mặc dù có những điểm mạnh, Ultralytics YOLO có một số hạn chế mà người dùng nên biết. Đầu tiên, mặc dù nó hỗ trợ một loạt các tác vụ, nó có thể không cung cấp cùng mức độ tùy chỉnh như các khung làm việc như Detectron2 cho các nhu cầu nghiên cứu chuyên biệt cao. Người dùng yêu cầu các thay đổi kiến trúc mới lạ có thể thấy phương pháp điều khiển bằng cấu hình là hạn chế.

Thứ hai, thư viện dựa rất nhiều vào PyTorch. Mặc dù PyTorch rất tuyệt vời, nhưng người dùng có các đường ống TensorFlow hiện có có thể gặp phải thách thức trong việc tích hợp. Việc chuyển đổi các mô hình giữa các khung làm việc có thể gây ra chi phí quá đầu và sự chênh lệch độ chính xác tiềm ẩn.

Thứ ba, mặc dù thư viện cung cấp các tùy chọn xuất rộng rãi, việc tối ưu hóa các mô hình cho các thiết bị biên cụ thể thường đòi hỏi thêm việc tinh chỉnh thủ công. Ví dụ, việc đạt hiệu suất tối ưu trên một chip ARM cụ thể có thể yêu cầu các kịch bản lượng tử hóa tùy chỉnh vượt ra ngoài những gì thư viện cung cấp ngay từ đầu.

Cuối cùng, chu kỳ phát hành nhanh chóng các phiên bản YOLO mới đôi khi có thể dẫn đến các thay đổi gây vỡ (breaking changes) hoặc các tính năng bị hủy bỏ. Người dùng phải cập nhật tài liệu mới nhất để đảm bảo tương thích với các cơ sở mã hiện có của họ.

## FAQ

### Q1: Công cụ này là gì và dành cho ai?
Đây là hướng dẫn toàn diện về cách sử dụng công cụ AI mã nguồn mở này một cách hiệu quả trong các môi trường sản xuất.

### Q2: Công cụ này so sánh với các giải pháp thay thế như thế nào?
Công cụ này mang lại những lợi thế độc đáo về hiệu suất, tính dễ sử dụng và hỗ trợ cộng đồng so với các giải pháp tương tự.

### Q3: Tôi có thể sử dụng công cụ này cho mục đích thương mại không?
Có, hầu hết các công cụ AI mã nguồn mở bao gồm công cụ này đều cho phép sử dụng thương mại theo giấy phép tương ứng của chúng.

### Q4: Yêu cầu phần cứng là gì?
Yêu cầu phần cứng thay đổi tùy theo kích thước mô hình và trường hợp sử dụng. Tăng tốc GPU được khuyến nghị để đạt hiệu suất tối ưu.

### Q5: Tôi khắc phục sự cố các vấn đề phổ biến như thế nào?
Hãy kiểm tra tài liệu chính thức, các vấn đề trên GitHub và các diễn đàn cộng đồng để tìm giải pháp cho các vấn đề phổ biến.

### Q6: Có đường cong học tập không?
Việc sử dụng cơ bản rất đơn giản, nhưng các tính năng nâng cao đòi hỏi phải hiểu các khái niệm nền tảng và cấu hình.

### Q7: Tôi có thể đóng góp cho dự án không?
Có, hầu hết các dự án AI mã nguồn mở đều hoan nghênh các đóng góp thông qua các pull request trên GitHub và báo cáo vấn đề.

### Q: Ultralytics YOLO có miễn phí sử dụng cho mục đích thương mại không?
Có, Ultralytics YOLO là phần mềm mã nguồn mở được phát hành theo giấy phép GPL-3.0. Tuy nhiên, người dùng nên lưu ý rằng giấy phép GPL yêu cầu rằng các tác phẩm phái sinh cũng phải được mã nguồn mở. Đối với các ứng dụng thương mại nơi việc giữ mã nguồn độc quyền là điều cần thiết, Ultralytics cung cấp các giấy phép thương mại với các điều khoản khác nhau. Luôn xem xét thỏa thuận giấy phép cụ thể trước khi triển khai trong môi trường thương mại.

### Q: Tôi có thể sử dụng Ultralytics YOLO cho xử lý video không?
Chắc chắn rồi. Thư viện hỗ trợ đầu vào video trực tiếp. Bạn có thể truyền đường dẫn tệp video hoặc chỉ số camera vào phương thức `predict`, và nó sẽ xử lý từng khung hình theo thứ tự. Đầu ra có thể được lưu dưới dạng video mới với các hộp giới hạn được chú thích.

```python
from ultralytics import YOLO

model = YOLO('yolov8n.pt')
results = model.predict(source='video.mp4', save=True, view_img=True)
```

### Q: Tôi huấn luyện Ultralytics YOLO trên tập dữ liệu tùy chỉnh của mình như thế nào?
Huấn luyện trên một tập dữ liệu tùy chỉnh bao gồm hai bước chính: chuẩn bị tập dữ liệu ở định dạng đúng và xác định một tệp cấu hình. Tập dữ liệu nên được tổ chức theo định dạng YOLO, với các ảnh trong thư mục `images` và nhãn trong thư mục `labels`. Một tệp YAML chỉ định các đường dẫn đến các thư mục này và tên lớp.

```yaml
path: ./my_dataset
train: images/train
val: images/val
test: images/test

nc: 5
names: ['class1', 'class2', 'class3', 'class4', 'class5']
```

Sau đó, bạn có thể huấn luyện bằng cách sử dụng:
```bash
yolo train data=my_dataset.yaml model=yolov8n.pt epochs=100
```

### Q: Ultralytics YOLO có hỗ trợ phân đoạn thể hiện (instance segmentation) không?
Có, Ultralytics YOLO hỗ trợ phân đoạn thể hiện bên cạnh phát hiện đối tượng và phân loại. Thư viện bao gồm các mô hình đã huấn luyện trước cho các tác vụ phân đoạn, và bạn có thể tinh chỉnh chúng trên các tập dữ liệu phân đoạn của riêng mình. API cho phân đoạn tương tự như phát hiện, trả về các mặt nạ cùng với các hộp giới hạn và xác suất lớp.

```python
from ultralytics import YOLO

# Tải mô hình phân đoạn
model = YOLO('yolov8n-seg.pt')

# Dự đoán trên một ảnh
results = model.predict(source='image.jpg', save=True)
```

### Q: Làm thế nào tôi có thể cải thiện tốc độ suy luận cho các ứng dụng thời gian thực?
Để cải thiện tốc độ suy luận, hãy cân nhắc các chiến lược sau:
1. **Sử dụng các mô hình nhỏ hơn**: Chọn YOLOv8n hoặc YOLOv8s thay vì các biến thể lớn hơn.
2. **Xuất sang các định dạng được tối ưu hóa**: Sử dụng TensorRT cho GPU NVIDIA hoặc ONNX Runtime cho CPU.
3. **Lượng tử hóa**: Áp dụng lượng tử hóa INT8 để giảm kích thước mô hình và tăng tốc độ.
4. **Xử lý theo lô**: Nếu độ trễ cho phép, hãy xử lý nhiều khung hình theo các lô.
5. **Tăng tốc phần cứng**: Sử dụng GPU hoặc các bộ tăng tốc AI chuyên dụng như TPUs hoặc NPUs.

```bash
# Ví dụ: Xuất với lượng tử hóa INT8 (yêu cầu dữ liệu hiệu chuẩn)
yolo export model=yolov8n.pt format=onnx int8=True
```


```bash
# Lệnh cài đặt cơ bản
pip install ultralytics ultralytics

# Xác minh cài đặt
Ultralytics Ultralytics --version
```

```python
# Ví dụ về đoạn mã sử dụng
import ultralytics_ultralytics

# Khởi tạo thành phần
component = Ultralytics_Ultralytics()
result = component.process(input_data)
print(result)
```

```yaml
# Ví dụ về cấu hình
ultralytics_ultralytics:
  model: default
  timeout: 30
  max_workers: 4
  log_level: info
```

## Kết luận

Ultralytics YOLO vẫn là một công cụ nền tảng trong hệ sinh thái thị giác máy tính, cung cấp một nền tảng mạnh mẽ, linh hoạt và thân thiện với người dùng cho phát hiện đối tượng, phân đoạn và phân loại. Tài liệu toàn diện, cộng đồng tích cực và các tùy chọn triển khai rộng rãi khiến nó phù hợp cho cả người mới bắt đầu và những người thực hành giàu kinh nghiệm. Bằng cách hiểu kiến trúc, quy trình cài đặt và các kỹ thuật tối ưu hóa của nó, các nhà phát triển có thể khai thác sức mạnh của nó để xây dựng các hệ thống thị giác hiệu quả và chính xác.

Khi bạn bắt đầu hành trình với Ultralytics YOLO, hãy nhớ khám phá các tài nguyên phong phú có sẵn trực tuyến, bao gồm tài liệu chính thức và các diễn đàn cộng đồng. Đối với những người quan tâm đến việc mở rộng cơ sở hạ tầng của họ, hãy cân nhắc tận dụng các dịch vụ đám mây cho việc huấn luyện và triển khai.

![DigitalOcean Logo](https://www.digitalocean.com/community/assets/logo-digitalocean.svg)

**Sẵn sàng triển khai các mô hình AI của bạn ở quy mô lớn?**  
Bắt đầu hành trình của bạn với **DigitalOcean** và nhận $200 tín dụng miễn phí!  
👉 [Yêu cầu Tín dụng $200 Của Bạn Tại Đây](https://m.do.co/c/eca87ac14ee0)

Tham gia cộng đồng của chúng tôi trên Telegram để nhận cập nhật, thảo luận và hỗ trợ:  
📢 **[Tham gia Nhóm DIBI8 trên Telegram](https://t.me/DIBI8_Group)**

---

*Thông báo Liên kết Chiếu hoa: Bài viết này chứa các liên kết tiếp thị liên kết. Nếu bạn mua hàng thông qua các liên kết này, chúng tôi có thể kiếm được một khoản hoa hồng mà không tốn thêm chi phí nào cho bạn. Điều này giúp hỗ trợ sự phát triển và bảo trì liên tục của nội dung AI mã nguồn mở trên dibi8.com.*