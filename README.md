#  Steam Game Recommendation System

Hệ thống gợi ý game Steam thông minh sử dụng **Hybrid Recommendation** (Collaborative Filtering + Content-Based + Hours-Based + Sentiment Analysis).

##  Tính năng

-  **Tìm kiếm game** với gợi ý tự động
-  **Gợi ý game tương tự** dựa trên AI
-  **Lọc theo thể loại** và giá
-  **Top game nổi bật** theo đánh giá
-  **Game mới phát hành** 
-  **Game được đánh giá cao**
-  **Video trailer** và mô tả chi tiết
-  **Dịch tự động** sang tiếng Việt

##  Demo trực tuyến



##  Cài đặt Local

### Yêu cầu
- Python 3.8+
- pip install

### Các bước cài đặt

```bash
# 1. Clone repository
git clone <your-repo-url>
cd END

# 2. Tạo virtual environment
python -m venv venv

# 3. Activate virtual environment
# Windows:
venv\Scripts\activate
# Linux/Mac:
source venv/bin/activate

# 4. Cài đặt dependencies
pip install -r requirements.txt

# 5. Chạy ứng dụng
streamlit run app.py
```

Ứng dụng sẽ mở tại: `http://localhost:8501`

##  Cấu trúc project

```
END/
├── app.py                          # Ứng dụng Streamlit chính
├── train_model_precompute.py       # Script training model
├── requirements.txt                # Dependencies Python
├── models/                         # Model files (pre-trained)
│   ├── games_metadata.pkl         # Metadata game (9MB)
│   ├── hybrid_similarity_top100.pkl # Ma trận similarity (5MB)
│   ├── tfidf_vectorizer.pkl       # TF-IDF vectorizer
│   └── top_games_list.pkl         # Danh sách top games
└── data/                          # Dataset (không cần cho deploy)
```

##  Phương pháp Hybrid Recommendation

Model kết hợp 4 yếu tố:

1. **Collaborative Filtering (60%)** - Dựa trên hành vi người dùng
2. **Hours-Based (25%)** - Thời gian chơi trung bình
3. **Content-Based (10%)** - Metadata (thể loại, developer, tags)
4. **Sentiment Analysis (5%)** - Đánh giá tích cực/tiêu cực

**Công thức:**
```
Hybrid_Score = 0.6×Collab + 0.25×Hours + 0.1×Content + 0.05×Sentiment
```

##  Training Model

Để train lại model từ đầu:

```bash
python train_model_precompute.py --data_dir data --output_dir models
```

**Dataset sử dụng:**
- `steam.csv` - Metadata game (~27k games)
- `steam-200k.csv` - User interaction data
- `steamspy_tag_data.csv` - Tags và thể loại
- `steam_requirements_data.csv` - Cấu hình yêu cầu
- `steam_media_data.csv` - Video/ảnh

##  Đánh giá Model

```bash
python evaluate_final.py
```

**Metrics:**
- Precision@K
- Recall@K
- NDCG@K
- Coverage
- Diversity

##  Deploy lên Cloud

### Streamlit Cloud (Khuyên dùng)

1. **Push code lên GitHub**
```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin <your-repo-url>
git push -u origin main
```

2. **Deploy trên Streamlit Cloud**
   - Truy cập [share.streamlit.io](https://share.streamlit.io)
   - Đăng nhập bằng GitHub
   - Chọn repository và branch
   - File chính: `app.py`
   - Click "Deploy"!

3. **Cấu hình (nếu cần)**
   - Python version: 3.9+
   - Requirements: tự động detect

### Hugging Face Spaces

```bash
# 1. Tạo Space mới tại huggingface.co/spaces
# 2. Clone về local
git clone https://huggingface.co/spaces/<username>/<space-name>

# 3. Copy files
cp -r models app.py requirements.txt <space-folder>/

# 4. Push lên
cd <space-folder>
git add .
git commit -m "Deploy app"
git push
```

##  Troubleshooting

### Lỗi: "File too large for GitHub"
- File > 100MB không thể push lên GitHub
- Giải pháp: Đã sử dụng `hybrid_similarity_top100.pkl` (5MB) thay vì bản đầy đủ (122MB)

### Lỗi: "Module not found"
```bash
pip install -r requirements.txt --upgrade
```

### App chậm khi khởi động
- Do load model lần đầu (~15MB)
- Sử dụng `@st.cache_resource` để cache

##  Cải tiến tương lai

- [ ] User authentication & personalized history
- [ ] Advanced embeddings (Sentence-BERT)
- [ ] Real-time collaborative filtering
- [ ] A/B testing framework
- [ ] Mobile responsive UI


