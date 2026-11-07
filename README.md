<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flashcard Học Từ Vựng</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', sans-serif; }
        :root {
            --primary: #4f46e5;
            --success: #10b981;
            --warning: #f59e0b;
            --danger: #ef4444;
            --light: #f8fafc;
            --card: #ffffff;
            --text: #1e293b;
            --shadow: 0 4px 20px rgba(0,0,0,0.08);
        }
        body { background: linear-gradient(135deg, #eef2ff, #f0fdf4); min-height: 100vh; color: var(--text); }
        
        /* ===== HEADER & NAV ===== */
        header { background: var(--primary); color: white; padding: 20px; text-align: center; }
        nav { position: sticky; top: 0; background: white; box-shadow: var(--shadow); z-index: 100; padding: 10px 0; }
        .nav-buttons { display: flex; overflow-x: auto; gap: 8px; padding: 0 15px; }
        .nav-btn { white-space: nowrap; padding: 10px 16px; border: none; border-radius: 20px; background: #e2e8f0; cursor: pointer; font-size: 14px; transition: 0.2s; }
        .nav-btn.active { background: var(--primary); color: white; }
        
        .container { max-width: 850px; margin: 0 auto; padding: 20px; }
        .tab-page { display: none; }
        .tab-page.active { display: block; animation: fadeIn 0.3s; }
        @keyframes fadeIn { from{opacity:0; transform: translateY(10px);} to{opacity:1; transform:0;} }
        
        h2 { font-size: 22px; margin-bottom: 20px; color: var(--primary); }
        h3 { font-size: 18px; margin: 16px 0 10px; }
        
        /* ===== THƯ MỤC ===== */
        .folder-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(150px, 1fr)); gap: 15px; }
        .folder-card { background: var(--card); border-radius: 12px; padding: 20px; text-align: center; box-shadow: var(--shadow); cursor: pointer; transition: 0.2s; }
        .folder-card:hover { transform: translateY(-3px); }
        .folder-icon { font-size: 40px; margin-bottom: 8px; }
        .add-card { border: 2px dashed #cbd5e1; background: transparent; color: #64748b; }
        
        /* ===== BỘ TỪ VỰNG ===== */
        .vocab-list { display: flex; flex-direction: column; gap: 10px; }
        .vocab-item { background: var(--card); border-radius: 10px; padding: 15px; box-shadow: var(--shadow); display: flex; justify-content: space-between; align-items: center; }
        .vocab-info h4 { font-size: 18px; }
        .vocab-info p { font-size: 13px; color: #64748b; margin-top: 3px; }
        .progress-bar { height: 6px; background: #e2e8f0; border-radius: 3px; margin-top: 6px; }
        .progress-fill { height: 100%; background: var(--success); border-radius: 3px; transition: 0.3s; }
        
        /* ===== FORM THÊM TỪ ===== */
        .form-card { background: var(--card); border-radius: 12px; padding: 25px; box-shadow: var(--shadow); margin-bottom: 20px; }
        .form-group { margin-bottom: 15px; }
        label { display: block; font-size: 14px; font-weight: 500; margin-bottom: 5px; }
        input, textarea, select { width: 100%; padding: 10px 12px; border: 1px solid #cbd5e1; border-radius: 8px; font-size: 15px; }
        input:focus, textarea:focus { outline: none; border-color: var(--primary); }
        .btn { padding: 10px 20px; border: none; border-radius: 8px; font-size: 15px; cursor: pointer; transition: 0.2s; }
        .btn-primary { background: var(--primary); color: white; }
        .btn-success { background: var(--success); color: white; }
        .btn-warning { background: var(--warning); color: white; }
        .btn-group { display: flex; gap: 10px; margin-top: 15px; }
        
        /* ===== FLASHCARD ===== */
        .flashcard-container { perspective: 1000px; margin: 30px auto; max-width: 450px; }
        .flashcard { width: 100%; height: 280px; position: relative; transform-style: preserve-3d; transition: transform 0.5s; cursor: pointer; }
        .flashcard.flipped { transform: rotateY(180deg); }
        .card-face { position: absolute; width: 100%; height: 100%; backface-visibility: hidden; border-radius: 16px; background: white; box-shadow: var(--shadow); padding: 25px; display: flex; flex-direction: column; justify-content: center; }
        .card-back { transform: rotateY(180deg); background: linear-gradient(135deg, #eef2ff, #f0fdf4); }
        .card-word { font-size: 36px; font-weight: bold; text-align: center; margin-bottom: 10px; }
        .card-phonetic { text-align: center; font-size: 18px; color: var(--primary); font-style: italic; margin-bottom: 20px; }
        .card-detail { margin-bottom: 8px; font-size: 15px; }
        .card-detail strong { color: var(--primary); }
        .card-nav { display: flex; justify-content: space-between; align-items: center; margin-top: 15px; }
        .rate-btn { padding: 8px 12px; border-radius: 6px; border: none; cursor: pointer; font-size: 13px; }
        .rate-again { background: #fee2e2; color: #b91c1c; }
        .rate-hard { background: #fed7aa; color: #c2410c; }
        .rate-good { background: #bfdbfe; color: #1e40af; }
        .rate-easy { background: #bbf7d0; color: #166534; }
        
        /* ===== MỤC TIÊU HÀNG NGÀY ===== */
        .goal-card { background: var(--card); border-radius: 12px; padding: 25px; box-shadow: var(--shadow); text-align: center; }
        .goal-number { font-size: 48px; font-weight: bold; color: var(--primary); }
        .goal-label { font-size: 15px; color: #64748b; margin: 8px 0 20px; }
        .goal-progress { height: 10px; background: #e2e8f0; border-radius: 5px; overflow: hidden; margin-bottom: 10px; }
        .goal-fill { height: 100%; background: linear-gradient(90deg, var(--primary), var(--success)); width: 0%; transition: width 0.5s; }
        .streak { display: flex; justify-content: center; gap: 10px; margin-top: 15px; }
        .streak-day { width: 36px; height: 36px; border-radius: 50%; background: #e2e8f0; display: flex; align-items: center; justify-content: center; font-size: 12px; }
        .streak-day.done { background: var(--success); color: white; }
        
        /* ===== TRÒ CHƠI ===== */
        .game-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 15px; }
        .game-card { background: var(--card); border-radius: 12px; padding: 25px; text-align: center; box-shadow: var(--shadow); cursor: pointer; transition: 0.2s; }
        .game-card:hover { transform: translateY(-3px); }
        .game-icon { font-size: 36px; margin-bottom: 10px; }
        .quiz-area { background: var(--card); border-radius: 12px; padding: 25px; box-shadow: var(--shadow); display: none; }
        .quiz-area.active { display: block; }
        .quiz-question { font-size: 20px; font-weight: 600; margin-bottom: 20px; text-align: center; }
        .quiz-options { display: flex; flex-direction: column; gap: 10px; }
        .quiz-option { padding: 15px; border: 2px solid #e2e8f0; border-radius: 8px; cursor: pointer; transition: 0.2s; text-align: center; }
        .quiz-option:hover { border-color: var(--primary); }
        .quiz-option.correct { border-color: var(--success); background: #dcfce7; }
        .quiz-option.wrong { border-color: var(--danger); background: #fee2e2; }
        
        /* ===== ẨN HIỆN ===== */
        .hidden { display: none !important; }
        .back-link { color: var(--primary); cursor: pointer; margin-bottom: 15px; display: inline-block; }
        .back-link:hover { text-decoration: underline; }
    </style>
</head>
<body>

<header>
    <h1>📚 Flashcard Học Từ Vựng</h1>
    <p id="today-date"></p>
</header>

<nav>
    <div class="nav-buttons">
        <button class="nav-btn active" onclick="showPage('folders')">📁 Thư mục</button>
        <button class="nav-btn" onclick="showPage('study')">📖 Học tập</button>
        <button class="nav-btn" onclick="showPage('add')">➕ Thêm từ</button>
        <button class="nav-btn" onclick="showPage('goal')">🎯 Mục tiêu</button>
        <button class="nav-btn" onclick="showPage('games')">🎮 Trò chơi</button>
    </div>
</nav>

<div class="container">

    <!-- ================================== -->
    <!-- ========== THƯ MỤC ============== -->
    <!-- ================================== -->
    <div id="folders" class="tab-page active">
        <h2>📁 Thư mục học tập</h2>
        <div class="folder-grid" id="folder-list">
            <!-- Thư mục sẽ thêm bằng JS -->
        </div>
        <br>
        <button class="btn btn-primary" onclick="createFolder()">➕ Tạo thư mục mới</button>
    </div>

    <!-- ================================== -->
    <!-- ======== BỘ TỪ VỰNG ============= -->
    <!-- ================================== -->
    <div id="vocab-set" class="tab-page">
        <span class="back-link" onclick="showPage('folders')">← Quay lại thư mục</span>
        <h2 id="folder-name-title">Bộ từ vựng</h2>
        <div class="vocab-list" id="vocab-list"></div>
        <br>
        <button class="btn btn-primary" onclick="showPage('add')">➕ Thêm từ mới</button>
        <button class="btn btn-success" style="margin-left:10px" onclick="startReview()">▶ Bắt đầu ôn tập</button>
    </div>

    <!-- ================================== -->
    <!-- ======= FLASHCARD HỌC TẬP ======= -->
    <!-- ================================== -->
    <div id="study" class="tab-page">
        <h2>📖 Ôn tập ngắt quãng</h2>
        <div class="flashcard-container" id="flashcard-container">
            <div class="flashcard" id="flashcard" onclick="flipCard()">
                <div class="card-face card-front">
                    <div class="card-word" id="card-front-word">—</div>
                    <div class="card-phonetic" id="card-phonetic">—</div>
                    <div style="text-align:center; color:#94a3b8; margin-top:20px;">👆 Chạm để lật thẻ</div>
                </div>
                <div class="card-face card-back">
                    <div class="card-detail"><strong>Loại từ:</strong> <span id="card-type">—</span></div>
                    <div class="card-detail"><strong>Nghĩa:</strong> <span id="card-meaning">—</span></div>
                    <div class="card-detail"><strong>Ví dụ:</strong> <span id="card-example">—</span></div>
                    <div class="card-detail"><strong>Phát âm:</strong> <span id="card-pronounce">🔊</span></div>
                    <div class="card-detail"><strong>Lưu ý:</strong> <span id="card-note">—</span></div>
                    <div class="card-detail"><strong>Đồng nghĩa:</strong> <span id="card-synonym">—</span></div>
                    <div class="card-detail"><strong>Trái nghĩa:</strong> <span id="card-antonym">—</span></div>
                </div>
            </div>
        </div>
        <div class="card-nav">
            <span id="card-counter">0 / 0</span>
            <div>
                <button class="rate-btn rate-again" onclick="rateCard(0)">Lại sau</button>
                <button class="rate-btn rate-hard" onclick="rateCard(1)">Khó</button>
                <button class="rate-btn rate-good" onclick="rateCard(2)">Bình thường</button>
                <button class="rate-btn rate-easy" onclick="rateCard(3)">Dễ</button>
            </div>
        </div>
    </div>

    <!-- ================================== -->
    <!-- ======== THÊM TỪ VỰNG ============ -->
    <!-- ================================== -->
    <div id="add" class="tab-page">
        <h2>➕ Thêm từ vựng mới</h2>
        <div class="form-card">
            <div class="form-group">
                <label>Chọn thư mục</label>
                <select id="select-folder"></select>
            </div>
            <div class="form-group">
                <label>Từ vựng *</label>
                <input type="text" id="word" placeholder="Nhập từ cần học">
            </div>
            <div class="form-group">
                <label>Phiên âm</label>
                <input type="text" id="phonetic" placeholder="Pinyin / Phiên âm">
            </div>
            <div class="form-group">
                <label>Loại từ</label>
                <input type="text" id="word-type" placeholder="Danh từ, động từ, tính từ...">
            </div>
            <div class="form-group">
                <label>Nghĩa *</label>
                <input type="text" id="meaning" placeholder="Nghĩa tiếng Việt">
            </div>
            <div class="form-group">
                <label>Phát âm / Ghi chú</label>
                <input type="text" id="pronounce" placeholder="Cách đọc">
            </div>
            <div class="form-group">
                <label>Ví dụ</label>
                <textarea id="example" rows="2" placeholder="Câu ví dụ sử dụng từ này"></textarea>
            </div>
            <div class="form-group">
                <label>Lưu ý</label>
                <textarea id="note" rows="2" placeholder="Điểm cần nhớ, cách dùng đặc biệt"></textarea>
            </div>
            <div class="form-group">
                <label>Từ đồng nghĩa</label>
                <input type="text" id="synonym" placeholder="Ngăn cách bằng dấu phẩy">
            </div>
            <div class="form-group">
                <label>Từ trái nghĩa</label>
                <input type="text" id="antonym" placeholder="Ngăn cách bằng dấu phẩy">
            </div>
            <div class="btn-group">
                <button class="btn btn-primary" onclick="saveWord()">💾 Lưu từ</button>
                <button class="btn" style="background:#e2e8f0" onclick="clearForm()">🗑️ Xóa form</button>
            </div>
        </div>
    </div>

    <!-- ================================== -->
    <!-- ======= MỤC TIÊU HÀNG NGÀY ======= -->
    <!-- ================================== -->
    <div id="goal" class="tab-page">
        <h2>🎯 Mục tiêu hàng ngày</h2>
        <div class="goal-card">
            <div class="goal-number" id="today-count">0</div>
            <div class="goal-label">Từ đã học hôm nay</div>
            <div class="goal-progress">
                <div class="goal-fill" id="goal-fill"></div>
            </div>
            <p id="goal-text">/ 20 từ mỗi ngày</p>
            
            <h3 style="margin-top:25px;">🔥 Chuỗi ngày liên tiếp</h3>
            <div class="streak" id="streak-days">
                <div class="streak-day">T2</div>
                <div class="streak-day">T3</div>
                <div class="streak-day">T4</div>
                <div class="streak-day">T5</div>
                <div class="streak-day">T6</div>
                <div class="streak-day">T7</div>
                <div class="streak-day">CN</div>
            </
# an.github.io