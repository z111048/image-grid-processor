# 圖片網格切割與處理 SOP

本文件記錄了將圖片細分為網格並按需求切割的完整過程，適合用於批量處理圖像。

## 工具需求

1. Python 3
2. 必要的 Python 套件：
   - Pillow
   - Matplotlib
   - ZipFile

## 過程步驟

### 1. 載入圖片

使用 Pillow 套件載入原始圖片。

```python
from PIL import Image

# 載入圖片
image_path = 'path_to_your_image.png'
image = Image.open(image_path)
```

### 2. 加入網格

在原始圖片上疊加網格，標記格子以便選擇。

```python
import matplotlib.pyplot as plt

# 設定網格參數
columns, rows = 16, 8  # 網格行列數
width, height = image.size
cell_width, cell_height = width / columns, height / rows

# 加入網格
fig, ax = plt.subplots()
ax.imshow(image)

# 畫網格線
for col in range(1, columns):
    ax.axvline(x=col * cell_width, color='blue', linestyle='--', linewidth=0.5)
for row in range(1, rows):
    ax.axhline(y=row * cell_height, color='blue', linestyle='--', linewidth=0.5)

ax.axis('off')
plt.show()
```

### 3. 選取並裁切區域

根據網格編號，選取特定區域裁切。

```python
def crop_by_grid_numbers(start_cell, end_cell, grid_columns, grid_rows, cell_width, cell_height, image):
    start_row = (start_cell - 1) // grid_columns
    start_col = (start_cell - 1) % grid_columns
    end_row = (end_cell - 1) // grid_columns
    end_col = (end_cell - 1) % grid_columns

    left = int(start_col * cell_width)
    upper = int(start_row * cell_height)
    right = int((end_col + 1) * cell_width)
    lower = int((end_row + 1) * cell_height)

    return image.crop((left, upper, right, lower))

# 選取範例
start_cell, end_cell = 2, 232
cropped_image = crop_by_grid_numbers(
    start_cell, end_cell, columns, rows, cell_width, cell_height, image
)

# 儲存裁切結果
cropped_image.save('cropped_image.png')
```

### 4. 批量裁切多個區域

針對多個區域進行裁切並保存。

```python
regions = [
    (2, 232), (10, 240), (17, 247), (25, 255),
    (258, 488), (266, 496), (273, 503), (281, 511)
]

cropped_images = []
for idx, (start, end) in enumerate(regions):
    cropped_image = crop_by_grid_numbers(
        start, end, columns, rows, cell_width, cell_height, image
    )
    output_path = f'cropped_image_{idx + 1}.png'
    cropped_image.save(output_path)
    cropped_images.append(output_path)
```

### 5. 壓縮裁切圖片

將裁切出的圖片壓縮成 ZIP 檔案。

```python
import zipfile

# 壓縮圖片
zip_file_path = 'cropped_images.zip'
with zipfile.ZipFile(zip_file_path, 'w') as zipf:
    for file_path in cropped_images:
        zipf.write(file_path, arcname=file_path.split('/')[-1])
```

### 6. 上傳 GitHub

1. 初始化 Git 儲存庫：

   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   ```

2. 新增遠端儲存庫：

   ```bash
   git remote add origin https://github.com/your-username/your-repo-name.git
   git branch -M main
   git push -u origin main
   ```

3. 撰寫 README 文件：
   - 說明此專案的用途和使用方式。
   - 提供操作步驟或程式碼範例。

## 注意事項

1. 確保網格大小與需求對應。
2. 選擇適當的壓縮比例以節省空間。
3. 可依需求擴展腳本功能，例如支持不同的網格分佈或批量處理多張圖片。

---

以上過程可根據需求進一步調整，若有任何問題請隨時聯繫我！
