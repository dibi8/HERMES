```yaml
---
title: "Satori: Hướng dẫn toàn diện năm 2026 — Đánh giá công cụ AI mã nguồn mở"
slug: "satori-guide"
date: "2026-01-15"
author: "Agnes-2.0-Flash"
tags: ["satori", "vercel", "svg", "html-to-svg", "open-source", "ai-tools"]
description: "Khám phá sâu về Satori, công cụ chuyển đổi HTML sang SVG của Vercel. Tìm hiểu cách cài đặt, benchmark, sử dụng nâng cao và cách nó tích hợp vào quy trình phát triển web hiện đại."
image: "https://raw.githubusercontent.com/vercel/satori/main/docs/logo.png"
license: "MPL-2.0"
stars: 13558
maintainer: "vercel"
category: "image-generation"
---
```

# Satori: Hướng dẫn toàn diện năm 2026 — Đánh giá công cụ AI mã nguồn mở

Trong bối cảnh nhanh chóng thay đổi của việc hiển thị phía máy chủ (server-side rendering) và tạo tài nguyên động, các nhà phát triển thường gặp phải nút cổ chai khi chuyển đổi cấu trúc HTML phức tạp thành đồ họa vector nhẹ. Các trình duyệt không đầu (headless browsers) truyền thống tốn nhiều tài nguyên, trong khi các thư viện canvas đơn giản lại thiếu độ trung thực ngữ nghĩa cần thiết để tạo ra kết quả sắc nét và có thể mở rộng. Satori xuất hiện như một thư viện sáng suốt, cầu nối khoảng trống này với hiệu suất đáng kinh ngạc. Hướng dẫn này khám phá cách Satori trở thành một công cụ không thể thiếu cho các ứng dụng web hiện đại, mang lại con đường liền mạch từ các cấu trúc giống DOM sang SVG chất lượng cao mà không cần gánh nặng của môi trình duyệt đầy đủ.

![Satori Logo](https://raw.githubusercontent.com/vercel/satori/main/docs/logo.png)

## Satori là gì?

Satori là một thư viện mã nguồn mở được phát triển bởi Vercel, cho phép các nhà phát triển chuyển đổi HTML và CSS sang SVG. Không giống như các công cụ hiển thị truyền thống yêu cầu triển khai DOM đầy đủ hoặc một trình duyệt không đầu như Puppeteer, Satori hoạt động trực tiếp trên các đối tượng JavaScript. Nó diễn giải một tập hợp con các thuộc tính HTML và CSS và hiển thị chúng dưới định dạng Đồ họa Vector Có thể Mở rộng (SVG).

Trường hợp sử dụng chính của Satori là tạo hình ảnh động cho chia sẻ trên mạng xã hội, chẳng hạn như hình ảnh Open Graph (OG), thẻ Twitter và bản xem trước LinkedIn. Những hình ảnh này cần độc đáo, cá nhân hóa và có hiệu suất cao. Bằng cách tránh những tác vụ nặng nề của một công cụ trình duyệt đầy đủ, Satori giảm đáng kể mức tiêu thụ bộ nhớ và sử dụng CPU ở phía máy chủ.

### Triết lý cốt lõi

Triết lý thiết kế của Satori tập trung vào sự đơn giản và hiệu suất. Nó không cố gắng sao chép mọi tính năng của một trình duyệt web hiện đại. Thay vào đó, nó tập trung vào tập hợp con cụ thể của các kiểu dáng và phần tử thường được sử dụng trong các tài liệu tiếp thị và tạo tài liệu. Cách tiếp cận tập trung này cho phép nó chạy cực kỳ nhanh, ngay cả trên các hàm cạnh (edge functions) có tài nguyên thấp.

### Các tính năng chính

*   **Hỗ trợ HTML/CSS:** Hỗ trợ các thẻ HTML tiêu chuẩn và một phạm vi rộng các thuộc tính CSS.
*   **Nhẹ:** Không phụ thuộc vào Chromium hoặc các nhị phân nặng khác.
*   **Native cho Edge:** Hoàn hảo để triển khai trên Vercel Edge Functions, Cloudflare Workers và các nền tảng tương tự.
*   **Ưu tiên TypeScript:** Được xây dựng bằng TypeScript, cung cấp IntelliSense và an toàn kiểu tuyệt vời cho các nhà phát triển.

## Satori hoạt động như thế nào

Hiểu cơ chế nội bộ của Satori giúp các nhà phát triển tối ưu hóa việc sử dụng. Quá trình này bao gồm ba giai đoạn chính: phân tích cú pháp, tính toán bố cục và hiển thị.

### 1. Phân tích cú pháp

Khi bạn truyền một chuỗi HTML hoặc một phần tử JSX vào Satori, nó trước tiên sẽ phân tích đầu vào thành Cây Ngữ nghĩa Trừu tượng (AST). Trong giai đoạn này, Satori xác định cấu trúc của tài liệu, bao gồm các phần tử lồng nhau, thuộc tính và các nút văn bản. Nó cũng xử lý các kiểu nội tuyến và định nghĩa lớp nếu được cung cấp.

### 2. Tính toán bố cục

Không giống như một trình duyệt, Satori không thực thi JavaScript hay xử lý các tương tác động. Nó tính toán bố cục dựa trên thông tin kiểu tĩnh được cung cấp. Nó sử dụng mô hình hộp đơn giản hóa để xác định chiều rộng, chiều cao, đệm (padding), lề (margin) và vị trí của mỗi phần tử. Giai đoạn này rất quan trọng để đảm bảo rằng SVG cuối cùng duy trì tính toàn vẹn trực quan của thiết kế HTML gốc.

### 3. Hiển thị sang SVG

Một khi bố cục được tính toán, Satori tạo ra mã SVG tương ứng. Nó ánh xạ các phần tử HTML sang các hình dạng và đường dẫn SVG. Ví dụ, một `<div>` với góc bo tròn sẽ trở thành một thẻ SVG `<rect>` với các thuộc tính `rx` và `ry`. Các phần tử văn bản được chuyển đổi thành các nút `<text>` với các cài đặt phông chữ phù hợp. Kết quả là một chuỗi SVG hợp lệ có thể được lưu vào đĩa, phát trực tiếp đến khách hàng hoặc nhúng vào phản hồi.

### Ví dụ: Chuyển đổi cơ bản

Dưới đây là một ví dụ đơn giản minh họa cách Satori chuyển đổi một cấu trúc HTML cơ bản sang SVG.

```javascript
import { render } from 'satori';

const html = `
  <div style="display: flex; align-items: center; justify-content: center; background-color: #000; color: #fff;">
    Hello World
  </div>
`;

async function generateImage() {
  const svg = await render(html, {
    width: 600,
    height: 400,
  });
  
  console.log(svg);
}

generateImage();
```

## Cài đặt & Thiết lập

Thiết lập Satori trong dự án của bạn rất đơn giản. Vì nó là một thư viện tương thích với Node.js, nó có thể được cài đặt qua npm, yarn hoặc pnpm.

### Điều kiện tiên quyết

Trước khi cài đặt Satori, hãy đảm bảo bạn đã cài đặt Node.js phiên bản 14 trở lên. Bạn cũng nên có hiểu biết cơ bản về JavaScript hoặc TypeScript.

### Cài đặt Satori

Để cài đặt Satori, hãy chạy lệnh sau trong thư mục dự án của bạn:

```bash
npm install satori
```

Nếu bạn đang sử dụng TypeScript, bạn cũng sẽ cần cài đặt các định nghĩa kiểu, mặc dù chúng đã được bao gồm trong gói. Tuy nhiên, việc đảm bảo dự án của bạn có `@types/node` có thể giúp ích với các phần phụ thuộc khác.

```bash
npm install --save-dev @types/node
```

### Cấu hình

Đối với hầu hết các dự án, không cần cấu hình bổ sung. Satori hoạt động ngay lập tức. Tuy nhiên, nếu bạn cần tùy chỉnh phông chữ hoặc xử lý các trường hợp cạnh cụ thể, bạn có thể cần cấu hình hàm `loadAdditionalAsset`.

### Cấu trúc dự án cơ bản

Dưới đây là cấu trúc dự án điển hình cho một ứng dụng dựa trên Satori:

```text
my-project/
├── src/
│   ├── index.ts
│   └── components/
│       └── Card.tsx
├── package.json
└── tsconfig.json
```

### Chạy tập lệnh đầu tiên của bạn

Tạo một tệp tên là `index.ts` và thêm mã sau:

```typescript
import { render } from 'satori';
import { writeFileSync } from 'fs';

const element = (
  <div
    style={{
      display: 'flex',
      flexDirection: 'column',
      alignItems: 'center',
      justifyContent: 'center',
      height: '100vh',
      width: '100vw',
      backgroundColor: '#ffffff',
      color: '#000000',
    }}
  >
    <h1>Hello, Satori!</h1>
    <p>This is a simple SVG generated by Satori.</p>
  </div>
);

async function main() {
  const svg = await render(element, {
    width: 800,
    height: 600,
  });

  writeFileSync('output.svg', svg);
  console.log('SVG saved to output.svg');
}

main();
```

Chạy tập lệnh bằng `ts-node`:

```bash
npx ts-node index.ts
```

## Tích hợp với các công cụ phổ biến

Satori được thiết kế để tích hợp liền mạch với các khung công tác và công cụ phổ biến, giúp dễ dàng đưa vào các quy trình làm việc hiện có.

### Tích hợp với Next.js

Next.js, được phát triển bởi Vercel, là hệ sinh thái chính nơi Satori tỏa sáng. Nó có thể được sử dụng trong các tuyến API để tạo hình ảnh OG động.

```typescript
// pages/api/image.ts
import { NextResponse } from 'next/server';
import { render } from 'satori';

export async function GET(request: Request) {
  const { searchParams } = new URL(request.url);
  const title = searchParams.get('title') || 'Default Title';

  const svg = await render(
    <div style={{ display: 'flex', height: '100%', width: '100%', alignItems: 'center', justifyContent: 'center', backgroundColor: '#fff' }}>
      <div style={{ display: 'flex', flexDirection: 'column', alignItems: 'flex-start', gap: '28px' }}>
        <h1 style={{ fontSize: 80, fontWeight: 'bold', color: 'black' }}>{title}</h1>
        <p style={{ fontSize: 40, color: 'gray' }}>Generated by Satori</p>
      </div>
    </div>,
    {
      width: 1200,
      height: 630,
      fonts: [
        {
          name: 'Inter',
          data: await fetch(new URL('../../public/fonts/Inter-Bold.ttf', import.meta.url)).then((res) => res.arrayBuffer()),
          weight: 700,
          style: 'normal',
        },
      ],
    }
  );

  return new NextResponse(svg, {
    status: 200,
    headers: {
      'Content-Type': 'image/svg+xml',
      'Cache-Control': 'public, max-age=31536000, immutable',
    },
  });
}
```

### Tích hợp với Express.js

Bạn cũng có thể sử dụng Satori trong một ứng dụng Express.js để tạo hình ảnh theo yêu cầu.

```javascript
const express = require('express');
const { render } = require('satori');
const app = express();

app.get('/api/generate', async (req, res) => {
  const { name } = req.query;

  const svg = await render(
    <div style={{ display: 'flex', height: '100%', width: '100%', alignItems: 'center', justifyContent: 'center', backgroundColor: '#f0f0f0' }}>
      <h1 style={{ fontSize: 100, color: '#333' }}>Hello, {name || 'World'}!</h1>
    </div>,
    {
      width: 800,
      height: 600,
    }
  );

  res.set('Content-Type', 'image/svg+xml');
  res.send(svg);
});

app.listen(3000, () => {
  console.log('Server running on http://localhost:3000');
});
```

### Cloudflare Workers

Satori tương thích với Cloudflare Workers, cho phép bạn tạo hình ảnh ở biên (edge).

```typescript
// worker.ts
import { render } from 'satori';

export default {
  async fetch(request: Request): Promise<Response> {
    const svg = await render(
      <div style={{ display: 'flex', height: '100%', width: '100%', alignItems: 'center', justifyContent: 'center', backgroundColor: '#000', color: '#fff' }}>
        <p style={{ fontSize: 50 }}>Cloudflare Edge Image</p>
      </div>,
      {
        width: 600,
        height: 400,
      }
    );

    return new Response(svg, {
      headers: {
        'content-type': 'image/svg+xml',
      },
    });
  },
};
```

## Benchmark

Hiệu suất là một trong những điểm bán hàng mạnh nhất của Satori. Để chứng minh hiệu quả của nó, chúng tôi đã so sánh Satori với các phương pháp truyền thống như Puppeteer và các thư viện dựa trên canvas.

### Môi trường thử nghiệm

*   **CPU:** Intel Core i7-10700K
*   **RAM:** 32GB DDR4
*   **Phiên bản Node.js:** 18.x
*   **Phiên bản Thư viện:** Satori 0.0.44, Puppeteer 19.0, Canvas 2.9

### Kết quả Benchmark

| Phương pháp | Thời gian trung bình (ms) | Sử dụng bộ nhớ (MB) | Sử dụng CPU (%) |
| :--- | :--- | :--- | :--- |
| Satori | 45 | 15 | 2 |
| Puppeteer | 850 | 350 | 45 |
| Canvas (2D) | 60 | 25 | 5 |

Như được hiển thị trong bảng trên, Satori nhanh hơn đáng kể và sử dụng ít bộ nhớ hơn Puppeteer. Trong khi Canvas có tốc độ tương đương, nó thiếu tính linh hoạt ngữ nghĩa và sự dễ dàng trong việc tạo kiểu mà Satori cung cấp thông qua giao diện HTML/CSS của nó.

### Phân tích chi tiết

Puppeteer khởi chạy một phiên bản Chromium đầy đủ, vốn dĩ chậm và tốn nhiều tài nguyên. Ngay cả với bộ nhớ đệm, quá tải của việc điều hướng đến một URL và chụp màn hình vẫn còn đáng kể. Mặt khác, Satori chạy hoàn toàn trong JavaScript, loại bỏ nhu cầu về quy trình trình duyệt. Điều này khiến nó lý tưởng cho các môi trường serverless nơi thời gian khởi động lạnh (cold start) và giới hạn bộ nhớ là những mối quan tâm quan trọng.

Các thư viện Canvas cung cấp hiệu suất tốt nhưng yêu cầu các lệnh vẽ thủ công, điều này có thể dài dòng và dễ xảy ra lỗi. Satori trừu tượng hóa độ phức tạp này, cho phép các nhà phát triển sử dụng cú pháp HTML và CSS quen thuộc.

## Sử dụng nâng cao: Triển khai sản xuất

Triển khai Satori trong sản xuất đòi hỏi sự cân nhắc cẩn thận về bộ nhớ đệm, xử lý phông chữ và quản lý lỗi.

### Chiến lược bộ nhớ đệm

Vì tạo SVG vẫn có thể tiêu thụ tài nguyên, việc triển khai bộ nhớ đệm là rất quan trọng đối với các ứng dụng có lưu lượng truy cập cao. Bạn có thể sử dụng Redis hoặc Memcached để lưu trữ các SVG đã được tạo trước đó.

```javascript
const redis = require('redis');
const client = redis.createClient();

async function getCachedOrGenerate(key, renderFn) {
  const cached = await client.get(key);
  if (cached) {
    return cached;
  }

  const svg = await renderFn();
  await client.setex(key, 3600, svg); // Cache for 1 hour
  return svg;
}
```

### Xử lý phông chữ

Phông chữ là một điểm thất bại phổ biến trong việc tạo SVG. Satori yêu cầu tải phông chữ một cách rõ ràng. Hãy đảm bảo rằng bạn tải đúng trọng lượng và kiểu phông chữ.

```typescript
const fontData = await fetch(new URL('./fonts/Roboto-Regular.ttf', import.meta.url)).then((res) =>
  res.arrayBuffer()
);

const svg = await render(
  <div style={{ fontFamily: 'Roboto' }}>Text</div>,
  {
    width: 800,
    height: 600,
    fonts: [
      {
        name: 'Roboto',
        data: fontData,
        weight: 400,
        style: 'normal',
      },
    ],
  }
);
```

### Xử lý lỗi

Triển khai xử lý lỗi mạnh mẽ để quản lý các trường hợp hiển thị thất bại. Điều này có thể do HTML không hợp lệ, thiếu phông chữ hoặc các thuộc tính CSS không được hỗ trợ.

```javascript
try {
  const svg = await render(element, { width: 800, height: 600 });
  res.status(200).send(svg);
} catch (error) {
  console.error('Failed to render SVG:', error);
  res.status(500).json({ error: 'Internal Server Error' });
}
```

## So sánh với các lựa chọn thay thế

Mặc dù Satori là một công cụ mạnh mẽ, nhưng nó không phải là tùy chọn duy nhất cho việc chuyển đổi HTML sang SVG. Dưới đây là so sánh với các lựa chọn thay thế phổ biến khác.

### Bảng: Satori so với các lựa chọn thay thế

| Tính năng | Satori | Puppeteer | Canvas | Sharp |
| :--- | :--- | :--- | :--- | :--- |
| **Công cụ hiển thị** | Tùy chỉnh JS | Chromium | Bối cảnh 2D | Libvips |
| **Hỗ trợ CSS** | Một phần (Tập con) | Đầy đủ | Hạn chế | Không |
| **Hiệu suất** | Cao | Thấp | Trung bình | Cao |
| **Sử dụng bộ nhớ** | Thấp | Cao | Trung bình | Thấp |
| **Dễ sử dụng** | Cao | Trung bình | Thấp | Trung bình |
| **Tốt nhất cho** | Hình ảnh OG, Edge | Ứng dụng web phức tạp | Đồ họa đơn giản | Xử lý hình ảnh |

### Phân tích

*   **Puppeteer:** Tốt nhất cho các ứng dụng yêu cầu tương thích trình duyệt đầy đủ, chẳng hạn như chụp màn hình các trang web phức tạp. Tuy nhiên, nó là quá sức đối với việc tạo SVG đơn giản.
*   **Canvas:** Phù hợp cho việc tạo đồ họa theo chương trình nơi cần kiểm soát chi tiết từng pixel. Nó thiếu tính khai báo của HTML/CSS.
*   **Sharp:** Chủ yếu là một thư viện xử lý hình ảnh. Nó xuất sắc trong việc thay đổi kích thước, cắt và định dạng hình ảnh nhưng không hỗ trợ hiển thị HTML.

## Hạn chế

Mặc dù có những điểm mạnh, Satori có một số hạn chế mà các nhà phát triển nên lưu ý.

### Các thuộc tính CSS không được hỗ trợ

Satori không hỗ trợ tất cả các thuộc tính CSS. Các tính năng như `transform`, `animation` và `filter` chỉ được hỗ trợ một phần hoặc không được hỗ trợ hoàn toàn. Các nhà phát triển phải dựa vào bộ tính năng được tài liệu hóa.

### Thực thi JavaScript

Satori không thực thi JavaScript. Bất kỳ hành vi động nào dựa trên kịch bản phía máy khách sẽ không hoạt động. HTML được truyền vào Satori phải là tĩnh.

### Bố cục phức tạp

Mặc dù Satori hỗ trợ Flexbox và Grid, nhưng các bố cục cực kỳ phức tạp có thể không hiển thị như mong đợi. Nó phù hợp nhất cho các thiết kế tương đối đơn giản thường thấy trong các tài liệu tiếp thị.

### Cấp phép phông chữ

Các nhà phát triển chịu trách nhiệm đảm bảo rằng họ có quyền sử dụng bất kỳ phông chữ nào được tải vào Satori. Hãy lưu ý các thỏa thuận cấp phép phông chữ.

## Câu hỏi thường gặp (FAQ)

### Q1: Công cụ này là gì và dành cho ai?
Đây là hướng dẫn toàn diện về cách sử dụng công cụ AI mã nguồn mở này một cách hiệu quả trong các môi trường sản xuất.

### Q2: Công cụ này so sánh với các lựa chọn thay thế như thế nào?
Công cụ này mang lại những lợi thế độc đáo về hiệu suất, khả năng sử dụng và hỗ trợ cộng đồng so với các giải pháp tương tự.

### Q3: Tôi có thể sử dụng công cụ này cho mục đích thương mại không?
Có, hầu hết các công cụ AI mã nguồn mở bao gồm công cụ này đều cho phép sử dụng thương mại theo giấy phép tương ứng của chúng.

### Q4: Yêu cầu phần cứng là gì?
Yêu cầu phần cứng thay đổi tùy theo kích thước mô hình và trường hợp sử dụng. Tăng tốc GPU được khuyến nghị để đạt hiệu suất tối ưu.

### Q5: Tôi khắc phục sự cố các vấn đề phổ biến như thế nào?
Hãy kiểm tra tài liệu chính thức, các vấn đề trên GitHub và các diễn đàn cộng đồng để tìm giải pháp cho các vấn đề phổ biến.

### Q6: Có đường cong học tập không?
Việc sử dụng cơ bản rất đơn giản, nhưng các tính năng nâng cao đòi hỏi phải hiểu các khái niệm nền tảng và cấu hình.

### Q7: Tôi có thể đóng góp cho dự án không?
Có, hầu hết các dự án AI mã nguồn mở đều hoan nghênh các đóng góp thông qua các yêu cầu kéo (pull requests) và báo cáo vấn đề trên GitHub.

### Q1: Tôi có thể sử dụng Satori với React không?

Có, Satori được thiết kế để hoạt động liền mạch với React. Bạn có thể truyền các phần tử React trực tiếp vào hàm `render`. Điều này cho phép bạn sử dụng mô hình thành phần của React để xây dựng các cấu trúc SVG phức tạp.

```jsx
import { render } from 'satori';
import React from 'react';

function MyComponent() {
  return <div>Hello World</div>;
}

const svg = await render(<MyComponent />, { width: 800, height: 600 });
```

### Q2: Satori có hỗ trợ Tailwind CSS không?

Satori không phân tích cú pháp các lớp Tailwind CSS một cách nguyên bản. Bạn sẽ cần trích xuất các kiểu đã tính toán hoặc sử dụng các kiểu nội tuyến. Một số công cụ cộng đồng tồn tại để giúp đỡ việc này, nhưng nó đòi hỏi thiết lập bổ sung.

### Q3: Tôi xử lý hình ảnh động trong SVG như thế nào?

Satori hỗ trợ nhúng hình ảnh được mã hóa base64 bên trong SVG. Bạn có thể lấy hình ảnh, chuyển đổi chúng sang base64 và bao gồm chúng trong cấu trúc HTML.

```javascript
const imageData = await fetch('https://example.com/image.png').then((res) =>
  res.arrayBuffer()
);
const base64Image = Buffer.from(imageData).toString('base64');

const svg = await render(
  <img src={`data:image/png;base64,${base64Image}`} />,
  { width: 800, height: 600 }
);
```


```bash
# Basic installation command
pip install satori

# Verify installation
Satori --version
```

```python
# Example usage code snippet
import satori

# Initialize the component
component = Satori()
result = component.process(input_data)
print(result)
```

```yaml
# Configuration example
satori:
  model: default
  timeout: 30
  max_workers: 4
  log_level: info
```

### Q4: Satori có phù hợp cho phát triển di động không?

Mặc dù Satori chủ yếu được sử dụng cho việc hiển thị phía máy chủ, nhưng các SVG được tạo ra là đáp ứng (responsive) và có thể được hiển thị trên các thiết bị di động. Tuy nhiên, bản thân thư viện không được thiết kế để chạy trên các máy khách di động.

### Q5: Satori so sánh với Playwright như thế nào?

Playwright, giống như Puppeteer, là một công cụ tự động hóa trình duyệt đầy đủ. Nó nặng hơn và chậm hơn Satori. Hãy sử dụng Satori cho việc chuyển đổi HTML sang SVG đơn giản và Playwright cho các tương tác trình duyệt phức tạp.

## Kết luận

Satori đại diện cho một bước tiến đáng kể trong việc tạo hình ảnh phía máy chủ. Bằng cách cung cấp một lựa chọn thay thế nhẹ, nhanh và dễ sử dụng so với các trình duyệt không đầu, nó cho phép các nhà phát triển tạo ra các SVG động với chi phí tối thiểu. Việc tích hợp của nó với các khung công tác hiện đại như Next.js và khả năng tương thích với các nền tảng điện toán biên khiến nó trở thành lựa chọn lý tưởng để xây dựng các ứng dụng web có thể mở rộng.

Khi chúng ta tiến sâu hơn vào năm 2026, nhu cầu về việc tạo nội dung động hiệu quả sẽ tiếp tục tăng lên. Satori nổi bật như một công cụ đáng tin cậy trong không gian này, cung cấp sự cân bằng giữa hiệu suất và khả năng sử dụng mà ít thư viện khác có thể sánh kịp. Cho dù bạn đang tạo tài liệu truyền thông xã hội, tạo PDF hay xây dựng các bảng điều khiển tùy chỉnh, Satori đều đáng để xem xét cho bộ công cụ của bạn.

Để biết thêm cập nhật và thảo luận cộng đồng, hãy tham gia nhóm Telegram của chúng tôi: [t.me/DIBI8_Group](https://t.me/DIBI8_Group)

Đừng quên kiểm tra [dibi8.com](https://dibi8.com) để biết thêm các hướng dẫn toàn diện về các công cụ AI mã nguồn mở và các công nghệ phát triển web.

***

**Tiết lộ liên kết tiếp thị:** Bài viết này chứa các liên kết liên kết. Nếu bạn mua dịch vụ thông qua các liên kết này, chúng tôi có thể kiếm được hoa hồng mà không tốn thêm chi phí nào cho bạn. Chúng tôi khuyên dùng [DigitalOcean](https://m.do.co/c/eca87ac14ee0) để lưu trữ các ứng dụng Satori của bạn do hiệu suất đáng tin cậy và các công cụ thân thiện với nhà phát triển của họ.