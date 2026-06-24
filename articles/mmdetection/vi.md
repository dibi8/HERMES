---
title: "mmdetection: Hướng dẫn toàn diện về khung phát hiện đối tượng của OpenMMLab"
description: "Khám phá mmdetection, công cụ phát hiện đối tượng mã nguồn mở hàng đầu của OpenMMLab. Tìm hiểu cách cài đặt, cấu hình, benchmark và sử dụng nâng cao dành cho nhà phát triển AI."
date: "2023-10-27"
slug: "/ai-tools/mmdetection-comprehensive-guide"
category: "ai-tools"
tags: ["object-detection", "computer-vision", "pytorch", "openmmlab", "deep-learning"]
github_repo: "open-mmlab/mmdetection"
stars: 32765
maintainer: "open-mmlab"
license: "Apache-2.0"
featureImage: "https://raw.githubusercontent.com/open-mmlab/mmdetection/master/resources/mmdet_logo.png"
lang: "en"
---

![Logo OpenMMLab](https://raw.githubusercontent.com/open-mmlab/mmdetection/master/resources/mmdet_logo.png)

Trong bối cảnh thị giác máy tính (computer vision) đang phát triển nhanh chóng, phát hiện đối tượng vẫn là một công nghệ nền tảng. Từ các phương tiện tự hành điều hướng trong môi trường đô thị phức tạp đến các hệ thống kiểm tra chất lượng trong sản xuất nhằm xác định lỗi trên dây chuyền lắp ráp, khả năng định vị chính xác và phân loại các đối tượng trong hình ảnh là cực kỳ quan trọng. Tuy nhiên, việc xây dựng các hệ thống này từ đầu là một nhiệm vụ đáng sợ, đòi hỏi chuyên môn sâu về kiến trúc học sâu, kỹ thuật tối ưu hóa và xử lý dữ liệu quy mô lớn. Đây là lúc **mmdetection** bước vào cuộc chơi, cung cấp một khung làm việc mạnh mẽ, có mô-đun và rất dễ mở rộng cho cả nhà nghiên cứu và kỹ sư.

Tại **dibi8.com**, chúng tôi hiểu rằng các nhà phát triển cần những công cụ đáng tin cậy để tăng tốc dự án AI của họ mà không cần phải phát minh lại bánh xe. Đó là lý do tại sao chúng tôi đã tổng hợp hướng dẫn toàn diện này về mmdetection, công cụ phát hiện đối tượng mã nguồn mở được phát triển bởi OpenMMLab. Với hơn 32.000 sao trên GitHub và một cộng đồng sôi động, mmdetection đã trở thành tiêu chuẩn trong ngành nhờ tính linh hoạt và hiệu suất của nó. Trong bài viết này, chúng ta sẽ đi sâu vào những yếu tố tạo nên sự thành công của nó, cách thiết lập, tích hợp với các công cụ khác và đánh giá tính ứng dụng thực tế của nó. Dù bạn là một kỹ sư ML giàu kinh nghiệm hay mới bắt đầu, hướng dẫn này sẽ trang bị cho bạn kiến thức để khai thác sức mạnh của mmdetection một cách hiệu quả.

## mmdetection là gì?

mmdetection là một công cụ phát hiện đối tượng mã nguồn mở được xây dựng trên nền tảng PyTorch. Được phát triển bởi dự án OpenMMLab, nó cung cấp một loạt các thuật toán tiên tiến cho phát hiện đối tượng, phân đoạn thể hiện (instance segmentation), phân đoạn toàn cảnh (panoptic segmentation) và phát hiện điểm mốc (keypoint detection). Công cụ này được thiết kế với tư duy mô-đun, cho phép người dùng dễ dàng kết hợp các thành phần như backbone, neck, head và hàm mất mát để tạo ra các đường ống phát hiện tùy chỉnh.

Một trong những tính năng nổi bật của mmdetection là hỗ trợ nhiều kiến trúc phổ biến, bao gồm Faster R-CNN, Mask R-CNN, chuỗi YOLO, RetinaNet và Cascade R-CNN, cùng nhiều kiến trúc khác. Tính linh hoạt này khiến nó phù hợp cho nhiều ứng dụng, từ lập mẫu nghiên cứu đến triển khai sản xuất. Ngoài ra, mmdetection được tài liệu hóa tốt và duy trì tích cực, đảm bảo rằng người dùng có quyền truy cập vào những tiến bộ mới nhất trong lĩnh vực này.

![Kiến trúc mmdetection](https://raw.githubusercontent.com/open-mmlab/mmdetection/master/resources/architecture.png)

Khung làm việc cũng nhấn mạnh khả năng tái lập, cung cấp các mô hình đã huấn luyện trước và các tệp cấu hình chi tiết cho phép người dùng sao chép các kết quả đã công bố với nỗ lực tối thiểu. Mức độ minh bạch này rất quan trọng cho cả nghiên cứu học thuật và các ứng dụng công nghiệp, nơi mà tính nhất quán và độ tin cậy là ưu tiên hàng đầu. Bằng cách tận dụng mmdetection, các nhà phát triển có thể tập trung vào việc giải quyết các thách thức cụ thể của miền ứng dụng thay vì bị vướng vào những phức tạp trong việc triển khai thuật toán.

## Cách mmdetection hoạt động

Về cốt lõi, mmdetection hoạt động thông qua một loạt các mô-đun liên kết với nhau để xử lý hình ảnh đầu vào nhằm phát hiện các đối tượng. Đường ống thường bao gồm nhiều giai đoạn: trích xuất đặc trưng, đề xuất vùng, phân loại và hồi quy hộp giới hạn (bounding box regression). Mỗi giai đoạn có thể được tùy chỉnh bằng cách sử dụng các thành phần khác nhau do công cụ cung cấp.

### Trích xuất đặc trưng

Bước đầu tiên trong phát hiện đối tượng là trích xuất các đặc trưng có ý nghĩa từ hình ảnh đầu vào. mmdetection hỗ trợ nhiều mạng backbone khác nhau, chẳng hạn như ResNet, DenseNet và các kiến trúc dựa trên Transformer như Swin Transformer. Các backbone này chịu trách nhiệm chuyển đổi dữ liệu pixel thô thành các bản đồ đặc trưng cấp cao nắm bắt thông tin không gian và ngữ nghĩa.

```python
# Ví dụ: Sử dụng ResNet-50 làm backbone
model = dict(
    type='FasterRCNN',
    backbone=dict(
        type='ResNet',
        depth=50,
        num_stages=4,
        out_indices=(0, 1, 2, 3),
        frozen_stages=1,
        norm_cfg=dict(type='BN', requires_grad=True),
        norm_eval=True,
        style='pytorch',
        init_cfg=dict(type='Pretrained', checkpoint='torchvision://resnet50')),
    neck=dict(
        type='FPN',
        in_channels=[256, 512, 1024, 2048],
        out_channels=256,
        num_outs=5),
    rpn_head=dict(
        type='RPNHead',
        in_channels=256,
        feat_channels=256,
        anchor_generator=dict(
            type='AnchorGenerator',
            scales=[8],
            ratios=[0.5, 1.0, 2.0],
            strides=[4, 8, 16, 32, 64]),
        bbox_coder=dict(
            type='DeltaXYWHBBoxCoder',
            target_means=[0., 0., 0., 0.],
            target_stds=[1., 1., 1., 1.]),
        loss_cls=dict(
            type='CrossEntropyLoss', use_sigmoid=True, loss_weight=1.0),
        loss_bbox=dict(type='L1Loss', loss_weight=1.0)),
    roi_head=dict(
        type='StandardRoIHead',
        bbox_roi_extractor=dict(
            type='SingleRoIExtractor',
            roi_layer=dict(type='RoIAlign', output_size=7, sampling_ratio=0),
            out_channels=256,
            featmap_strides=[4, 8, 16, 32]),
        bbox_head=dict(
            type='Shared2FCBBoxHead',
            in_channels=256,
            fc_out_channels=1024,
            roi_feat_size=7,
            num_classes=80,
            bbox_coder=dict(
                type='DeltaXYWHBBoxCoder',
                target_means=[0., 0., 0., 0.],
                target_stds=[0.1, 0.1, 0.2, 0.2]),
            reg_class_agnostic=False,
            loss_cls=dict(
                type='CrossEntropyLoss', use_softmax=True, loss_weight=1.0),
            loss_bbox=dict(type='L1Loss', loss_weight=1.0))))
```

### Mạng Đề xuất Vùng (RPN)

Sau khi trích xuất đặc trưng, bước tiếp theo là tạo ra các vùng ứng viên nơi đối tượng có thể tồn tại. mmdetection triển khai Mạng Đề xuất Vùng (RPN) cho các bộ phát hiện hai giai đoạn như Faster R-CNN, đề xuất các vùng dựa trên các hộp neo (anchor boxes) đã học. Đối với các bộ phát hiện một giai đoạn như YOLO, mạng trực tiếp dự đoán các tọa độ hộp giới hạn và xác suất lớp.

### Phân loại và Hồi quy Hộp giới hạn

Sau khi đề xuất các vùng, mô hình phân loại mỗi vùng vào các danh mục đối tượng cụ thể và tinh chỉnh các tọa độ hộp giới hạn để cải thiện độ chính xác định vị. Điều này được thực hiện thông qua các lớp kết nối đầy đủ (fully connected layers) và các phép toán tích chập, tùy thuộc vào kiến trúc.

![Đường ống Phát hiện](https://raw.githubusercontent.com/open-mmlab/mmdetection/master/resources/pipeline.png)

Tính mô-đun của mmdetection cho phép người dùng thay thế bất kỳ thành phần nào một cách liền mạch. Ví dụ, bạn có thể thay thế backbone bằng một kiến trúc mới hơn hoặc thay đổi hàm mất mát để phù hợp hơn với tập dữ liệu của bạn. Sự linh hoạt này là một trong những lý do chính khiến mmdetection được chấp nhận rộng rãi trong cả học thuật và công nghiệp.

## Cài đặt & Thiết lập (<= 5 phút)

Bắt đầu với mmdetection khá đơn giản nhờ tài liệu toàn diện và hướng dẫn cài đặt dễ hiểu của nó. Dưới đây, chúng tôi phác thảo các bước để cài đặt mmdetection trên máy cục bộ hoặc môi trường đám mây.

### Yêu cầu tiên quyết

Trước khi cài đặt mmdetection, hãy đảm bảo bạn có các yêu cầu tiên quyết sau:

- Python 3.7+
- PyTorch 1.7+
- Bộ công cụ CUDA (nếu sử dụng GPU)
- GCC 5+

### Hướng dẫn cài đặt từng bước

1. **Sao chép kho lưu trữ**:

```bash
git clone https://github.com/open-mmlab/mmdetection.git
cd mmdetection
```

2. **Tạo môi trường ảo**:

```bash
conda create -n mmdet python=3.8 -y
conda activate mmdet
```

3. **Cài đặt các phụ thuộc**:

```bash
pip install -r requirements/build.txt
pip install -v -e .
```

4. **Xác minh cài đặt**:

```bash
python -c "import mmdet; print(mmdet.__version__)"
```

Nếu cài đặt thành công, bạn sẽ thấy số phiên bản được in ra màn hình console. Bây giờ bạn có thể tiến hành huấn luyện mô hình hoặc chạy suy luận (inference) bằng các tập lệnh được cấu hình sẵn.

## Tích hợp với 3-5 Công cụ

mmdetection tích hợp liền mạch với nhiều công cụ và khung làm việc phổ biến khác, nâng cao tính hữu ích của nó trong các quy trình làm việc đa dạng.

### TensorBoard cho Trực quan hóa

TensorBoard được sử dụng rộng rãi để trực quan hóa các chỉ số huấn luyện. mmdetection hỗ trợ TensorBoard ngay từ đầu, cho phép bạn theo dõi các đường cong mất mát (loss curves), tốc độ học và các chỉ số quan trọng khác trong quá trình huấn luyện.

```bash
# Chạy huấn luyện với ghi nhật ký TensorBoard
python tools/train.py configs/faster_rcnn/faster_rcnn_r50_fpn_1x_coco.py --work-dir ./work_dirs/faster_rcnn --tensorboard
```

### MMCV cho Tăng cường Dữ liệu

MMCV, một dự án khác của OpenMMLab, cung cấp các tiện ích cho tăng cường dữ liệu và xử lý trước. Nó bổ sung cho mmdetection bằng cách cung cấp một bộ chuyển đổi (transforms) phong phú có thể được áp dụng cho các tập dữ liệu.

```python
# Ví dụ: Áp dụng tăng cường dữ liệu với MMCV
from mmcv.transforms import LoadImageFromFile, Resize, RandomFlip, PackDetInputs

train_pipeline = [
    dict(type='LoadImageFromFile'),
    dict(type='Resize', scale=(1333, 800)),
    dict(type='RandomFlip', prob=0.5),
    dict(type='PackDetInputs')
]
```

### ONNX cho Triển khai

Để triển khai các mô hình trong môi trường sản xuất, mmdetection hỗ trợ xuất mô hình sang định dạng ONNX, sau đó có thể chạy trên nhiều nền tảng khác nhau, bao gồm các thiết bị biên (edge devices).

```bash
# Xuất mô hình sang ONNX
python tools/deployment/pytorch2onnx.py \
    configs/faster_rcnn/faster_rcnn_r50_fpn_1x_coco.py \
    checkpoints/faster_rcnn_r50_fpn_1x_coco.pth \
    --output-file faster_rcnn.onnx \
    --verify
```

### Docker cho Khả năng Tái lập

Docker đảm bảo môi trường của bạn nhất quán trên các máy khác nhau. mmdetection cung cấp các hình ảnh Docker chính thức, giúp dễ dàng tái lập các thí nghiệm.

```bash
# Kéo và chạy container Docker
docker pull openmmlab/mmdetection:v2.24.0
docker run -it --gpus all -v $(pwd):/workspace openmmlab/mmdetection:v2.24.0 bash
```

## Kết quả Benchmark / Trường hợp Sử dụng Thực tế (với BẢNG)

Để đánh giá hiệu quả của mmdetection, chúng ta sẽ xem xét một số kết quả benchmark và trường hợp sử dụng thực tế.

| Mô hình | Tập dữ liệu | AP (Test) | Tốc độ (FPS) |
|-------|---------|-----------|-------------|
| Faster R-CNN | COCO | 39.4     | 25          |
| Mask R-CNN   | COCO | 36.5     | 20          |
| YOLOv5       | COCO | 37.0     | 80          |
| RetinaNet    | COCO | 36.4     | 45          |

*Bảng 1: Kết quả Benchmark trên Tập dữ liệu COCO*

Những kết quả này chứng minh rằng mmdetection hỗ trợ một loạt các mô hình, mỗi mô hình được tối ưu hóa cho các sự đánh đổi khác nhau giữa độ chính xác và tốc độ. Ví dụ, YOLOv5 cung cấp thời gian suy luận nhanh hơn, phù hợp cho các ứng dụng thời gian thực, trong khi Faster R-CNN cung cấp độ chính xác cao hơn cho phân tích ngoại tuyến.

Trong các kịch bản thực tế, mmdetection đã được sử dụng trong nhiều ngành công nghiệp:

- **Y tế**: Phát hiện khối u trong các quét hình ảnh y tế.
- **Bán lẻ**: Tự động hóa quản lý hàng tồn kho bằng cách nhận dạng sản phẩm trên kệ.
- **An ninh**: Xác định các hoạt động đáng ngờ trong video giám sát.

## Sử dụng Nâng cao / Sản xuất

Đối với các triển khai sản xuất, việc tối ưu hóa các mô hình mmdetection là rất quan trọng. Các kỹ thuật như lượng tử hóa (quantization), tỉa thưa (pruning) và triễn thức tri thức (knowledge distillation) có thể giảm đáng kể kích thước mô hình và cải thiện tốc độ suy luận mà không làm giảm độ chính xác.

### Lượng tử hóa (Quantization)

Lượng tử hóa giảm độ chính xác của trọng số mô hình từ float32 xuống int8, dẫn đến các mô hình nhỏ hơn và tốc độ suy luận nhanh hơn.

```bash
# Áp dụng lượng tử hóa
python tools/analysis_tools/quantize.py \
    configs/faster_rcnn/faster_rcnn_r50_fpn_1x_coco.py \
    checkpoints/faster_rcnn_r50_fpn_1x_coco.pth \
    --quantize
```

### Tỉa thưa (Pruning)

Tỉa thưa loại bỏ các nơ-ron dư thừa khỏi mạng,进一步 giảm yêu cầu tính toán.

```bash
# Áp dụng tỉa thưa
python tools/analysis_tools/prune.py \
    configs/faster_rcnn/faster_rcnn_r50_fpn_1x_coco.py \
    checkpoints/faster_rcnn_r50_fpn_1x_coco.pth \
    --prune
```

### Triễn thức Tri thức (Knowledge Distillation)

Triễn thức tri thức chuyển giao kiến thức từ một mô hình giáo viên (teacher model) lớn hơn sang một mô hình học sinh (student model) nhỏ hơn, cải thiện hiệu quả.

```bash
# Thực hiện triễn thức tri thức
python tools/train_distill.py \
    configs/faster_rcnn/faster_rcnn_r50_fpn_1x_coco.py \
    checkpoints/teacher_model.pth \
    --student-config configs/faster_rcnn/student_config.py
```

## So sánh với Các Giải pháp Thay thế (BẢNG, >=3 đối thủ cạnh tranh)

Mặc dù mmdetection là một công cụ mạnh mẽ, nhưng điều quan trọng là phải xem xét các giải pháp thay thế khi chọn khung làm việc cho dự án của bạn.

| Khung làm việc | Điểm mạnh | Điểm yếu |
|-----------|-----------|------------|
| mmdetection | Mô-đun, tài liệu mở rộng, hỗ trợ cộng đồng mạnh mẽ | Đường cong học tập dốc hơn đối với người mới bắt đầu |
| Detectron2 | Được Facebook hậu thuẫn, xuất sắc cho nghiên cứu | Ít linh hoạt hơn cho các thiết lập tùy chỉnh |
| TorchVision | API đơn giản, tốt cho các tác vụ cơ bản | Hạn chế các tính năng nâng cao |
| TensorFlow Object Detection API | Tích hợp mạnh mẽ với hệ sinh thái TF | Tốc độ phát triển chậm hơn |

*Bảng 2: So sánh với Các Đối thủ Cạnh tranh*

Mỗi khung làm việc đều có những điểm mạnh và điểm yếu riêng. mmdetection nổi bật nhờ tính mô-đun và tài liệu mở rộng, khiến nó lý tưởng cho những người dùng cần sự linh hoạt và tùy chỉnh. Detectron2, được hậu thuẫn bởi Facebook, rất tuyệt vời cho mục đích nghiên cứu nhưng có thể đòi hỏi nhiều nỗ lực hơn để thích nghi cho việc sử dụng sản xuất. TorchVision mang lại sự đơn giản nhưng thiếu các tính năng nâng cao, trong khi API Phát hiện Đối tượng của TensorFlow cung cấp tích hợp mạnh mẽ với hệ sinh thái TF nhưng gần đây đã chứng kiến tốc độ phát triển chậm lại.

## Hạn chế / Đánh giá Trung thực

Mặc dù có nhiều lợi thế, nhưng mmdetection không phải là không có hạn chế. Một thách thức phổ biến là độ phức tạp trong việc cấu hình và tinh chỉnh các siêu tham số, đặc biệt là đối với người mới bắt đầu. Ngoài ra, mặc dù khung làm việc hỗ trợ một loạt các mô hình, nhưng một số kiến trúc ít phổ biến hơn có thể đòi hỏi thêm nỗ lực để triển khai.

Một cân nhắc khác là mức tiêu thụ tài nguyên. Huấn luyện các mô hình lớn trên hình ảnh độ phân giải cao có thể tốn kém về mặt tính toán, đòi hỏi quyền truy cập vào các GPU mạnh mẽ hoặc tài nguyên điện toán đám mây. Người dùng nên đánh giá cẩn thận khả năng phần cứng của mình trước khi bắt đầu các dự án quy mô lớn.

Cuối cùng, mặc dù mmdetection được duy trì tích cực, nhưng việc cập nhật các bản cập nhật và sửa lỗi mới nhất có thể đòi hỏi sự tham gia thường xuyên với cộng đồng. Việc cập nhật thông tin về các bản phát hành mới và các thực tiễn tốt nhất là rất quan trọng để tối đa hóa tiềm năng của khung làm việc.

## Câu hỏi Thường gặp (FAQ)

### Q1: mmdetection chủ yếu được sử dụng cho mục đích gì?
mmdetection chủ yếu được sử dụng cho các tác vụ phát hiện đối tượng, phân đoạn thể hiện và phát hiện điểm mốc trong các ứng dụng thị giác máy tính.

### Q2: mmdetection so sánh như thế nào với các khung làm việc khác như Detectron2?
mmdetection cung cấp tính mô-đun và tùy chọn tùy chỉnh lớn hơn so với Detectron2, khiến nó phù hợp hơn cho những người dùng cần sự linh hoạt trong quy trình làm việc của họ.

### Q3: Tôi có thể sử dụng mmdetection cho các ứng dụng thời gian thực không?
Có, mmdetection hỗ trợ các mô hình nhẹ như YOLOv5, được tối ưu hóa cho suy luận thời gian thực trên các thiết bị biên.

### Q4: mmdetection có phù hợp cho người mới bắt đầu không?
Mặc dù mmdetection rất mạnh mẽ, nhưng nó có đường cong học tập khá dốc. Người mới bắt đầu có thể được hưởng lợi từ việc bắt đầu với các khung làm việc đơn giản hơn như TorchVision trước khi chuyển sang mmdetection.

### Q5: Tôi có thể tìm các mô hình đã huấn luyện trước cho mmdetection ở đâu?
Các mô hình đã huấn luyện trước có sẵn trong phần kho mô hình (model zoo) của kho lưu trữ GitHub mmdetection và có thể được tải xuống trực tiếp để sử dụng trong các dự án của bạn.

## Nguồn & Đọc Thêm

Đối với những ai quan tâm đến việc đi sâu hơn vào mmdetection, dưới đây là một số tài nguyên hữu ích:

- [Tài liệu Chính thức](https://mmdetection.readthedocs.io/)
- [Kho lưu trữ GitHub](https://github.com/open-mmlab/mmdetection)
- [Blog OpenMMLab](https://openmmlab.medium.com/)

Ngoài ra, việc khám phá các dự án liên quan như MMCV và MMDetection3D có thể cung cấp thêm thông tin chi tiết về các kỹ thuật thị giác máy tính nâng cao.


```bash
# Cài đặt MMDetection
pip install mmcv==2.0.1 mmdet==3.0.0
```
```python
# Suy luận MMDetection
from mmdet.apis import init_detector, inference_detector

config_file = "configs/yolov5/yolov5_d5_8xb16-300e_coco.py"
checkpoint_file = "yolov5_d5_8xb16-300e_coco.pth"
model = init_detector(config_file, checkpoint=checkpoint_file, device="cuda:0")
result = inference_detector(model, "test_image.jpg")
```


```python
# Huấn luyện với cấu hình MMCV
from mmengine.config import Config
from mmdet.engine import Runner

cfg = Config.fromfile("configs/yolov5/yolov5_d5_8xb16-300e_coco.py")
runner = Runner(
    model=cfg.model,
    work_dir=cfg.work_dir,
    train_cfg=cfg.train_cfg,
    optim_wrapper=cfg.optim_wrapper,
    train_dataloader=cfg.train_dataloader,
    val_dataloader=cfg.val_dataloader,
    val_cfg=cfg.val_cfg,
    val_evaluator=cfg.eval_cfg,
    default_scope="mmdet",
)
runner.train()
```
## Kết luận: CTA + dibi8.com + Telegram

Tóm lại, mmdetection là một khung làm việc linh hoạt và mạnh mẽ, trao quyền cho các nhà phát triển xây dựng các hệ thống phát hiện đối tượng tinh vi một cách hiệu quả. Thiết kế mô-đun, tài liệu mở rộng và cộng đồng hoạt động tích cực của nó khiến nó trở thành một tài sản vô giá cho cả môi trường nghiên cứu và sản xuất. Tại **dibi8.com**, chúng tôi tin rằng việc có quyền truy cập vào các công cụ đáng tin cậy như mmdetection có thể đẩy nhanh đáng kể hành trình AI của bạn.

Để cập nhật các bài viết mới nhất, hướng dẫn và thảo luận cộng đồng, hãy tham gia nhóm Telegram của chúng tôi: [t.me/DIBI8_Group](https://t.me/DIBI8_Group). Đừng quên khám phá thêm các tài nguyên khác trên [dibi8.com](https://dibi8.com) cho các hướng dẫn toàn diện về các công cụ và công nghệ AI.

*Thông báo Liên kết Chiết khấu: Một số liên kết trong bài viết này có thể là liên kết chiết khấu, có nghĩa là chúng tôi có thể kiếm được một khoản hoa hồng nhỏ nếu bạn thực hiện mua hàng thông qua chúng. Điều này giúp hỗ trợ công việc của chúng tôi trong việc cung cấp nội dung miễn phí, chất lượng cao.*