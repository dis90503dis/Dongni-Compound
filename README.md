# 東泥大院 — 建案形象官網

Vue 3 單頁式房地產建案落地頁（台南東山），含主視覺輪播、建案資訊、地圖、預約表單與隱私權政策。

## 技術棧

- **Vue 3**（`<script setup>`）+ **Vite 6**
- Bootstrap 5、Tailwind CSS、SCSS
- AOS 捲動動畫、vue-toastification
- Google reCAPTCHA v2、GTM（vue-gtm）
- vite-plugin-imagemin 圖片壓縮

## 執行

```bash
npm install
npm run dev     # 開發（開放 LAN，方便手機測試）
npm run build   # 打包至 dist/
```

## 結構

```
src/
├── App.vue        # 主頁面
├── components/    # Carousel、Form、SidebarMenu、RecaptchaField…
├── section/form/  # 地圖、建案資訊、聯絡資訊、隱私權
├── info/          # 案名、電話、地址、GTM、reCAPTCHA 等設定
└── assets/        # 各段落圖片與樣式
```

## 表單

`src/components/Form.vue` 驗證欄位與 reCAPTCHA 後，
透過 Google Apps Script 紀錄資料並串接預約系統 API，成功後以 toast 提示。
