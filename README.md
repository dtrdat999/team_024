# MindFeng 🧭
**AI Chatbot Tư Vấn Phong Thủy & Chọn Căn Hộ theo Mệnh**

> Gate G1 — Mã đề tài: A120K-154 | Deadline: 23:50 ngày 7/6/2026

---

## Tổng quan

MindFeng là nền tảng web AI tư vấn phong thủy bất động sản cho người mua nhà tại Việt Nam. Hệ thống kết hợp **Rule Engine Bát trạch** (tính nhất quán) + **RAG kho tri thức** (chiều sâu) + **Claude LLM** (ngôn ngữ tự nhiên) để đưa ra tư vấn cá nhân hóa theo mệnh cục, gắn với dữ liệu căn hộ thực tế.

### Bài toán
Phong thủy là yếu tố quan trọng với >70% người mua nhà tại Việt Nam, nhưng:
- Kiến thức trên mạng rải rác, mâu thuẫn
- Sales không đủ chuyên môn tư vấn nhất quán  
- Chuyên gia giỏi khan hiếm, phí cao (3–10 triệu/lần)

### Giải pháp
AI tư vấn phong thủy **24/7**, **nhất quán**, **cá nhân hóa theo mệnh**, gắn với **dữ liệu căn hộ thực tế**.

---

## Gate G1 Deliverables

| # | Deliverable | File | Status |
|---|-------------|------|--------|
| 1 | **1-Page Brief** | `docs/brief_1page.html` | ✅ |
| 2 | **PRD đầy đủ** | `docs/prd_full.html` | ✅ |
| 3 | **Wireframe/UI Flow** | `docs/wireframe_ui_flow.html` | ✅ |
| 4 | **GitHub Repo** | Repository này | ✅ |

---

## Tech Stack

| Layer | Technology | Lý do |
|-------|-----------|-------|
| Frontend | Next.js 14 + Tailwind CSS | App Router, SSR, deploy Vercel 1-click |
| Auth | Supabase Auth | JWT + OAuth Google, Row-Level Security |
| Backend | FastAPI (Python) | Async, Anthropic SDK integration |
| Database | Supabase PostgreSQL + pgvector | RAG embeddings + Realtime |
| AI Core | Claude claude-sonnet-4-20250514 | Tool calling, multilingual, streaming |
| Embeddings | text-embedding-3-small (OpenAI) | Chi phí thấp, đủ chất lượng RAG |
| Deploy FE | Vercel | CI/CD auto từ GitHub |
| Deploy BE | Railway | Docker-ready, zero-ops |

---

## Kiến trúc AI (3 layer)

```
User Input
    ↓
[Layer 1] Rule Engine — Bát trạch 8×8 lookup → Điểm 1–10 (<50ms)
    ↓
[Layer 2] RAG Lookup — pgvector similarity search → Top-5 chunks (<200ms)
    ↓
[Layer 3] Claude LLM — Tổng hợp streaming → Response 3 phần (<4s p95)
    ↓
Output: Tóm tắt 1 câu → Chi tiết → Lưu ý thực tế
```

---

## Personas

| Persona | Vai trò | Pain Point | Mong muốn |
|---------|---------|-----------|-----------|
| Nguyễn Thị Linh (32t) | Người mua lần đầu | Sợ mua sai hướng, thông tin mâu thuẫn | Giải thích rõ lý do, nhất quán |
| Trần Quốc Minh (43t) | Nhà đầu tư BĐS | Tra phong thủy thủ công mất thời gian | So sánh nhanh, tóm tắt ngắn gọn |
| Phạm Thu Hoa (28t) | Sales agent | Mất momentum khi khách hỏi phong thủy | Tool real-time, response <5s |

---

## MVP Features

### Sprint 1 — Foundation (Tuần 1–2)
- [x] Auth & Profile Setup Wizard (2 bước, tính cung phi real-time)
- [x] Rule Engine Bát trạch (64 tổ hợp, 100% unit tested)
- [x] Admin CRUD dự án & căn hộ (đơn lẻ + CSV import)
- [x] Deploy skeleton: Vercel + Railway

### Sprint 2 — AI Core (Tuần 2–3)
- [ ] AI Chat streaming (Claude API, p95 <4s)
- [ ] RAG Pipeline (upload PDF → chunk → embed → pgvector)
- [ ] Feng Score Badge trên card căn hộ
- [ ] AI Comparison Card (2–3 căn gợi ý)

### Sprint 3 — Business Layer (Tuần 3–4)
- [ ] Lead Capture + Sales Dashboard
- [ ] Email notification (Resend)
- [ ] Mobile Responsive + PWA
- [ ] UI/UX polish + Load testing

---

## Database Schema

```sql
users           -- Supabase Auth (id, email, role)
user_profiles   -- Mệnh cục + nhu cầu (cung_phi, ngu_hanh, huong_tot[], budget)
projects        -- Dự án BĐS (id, name, address, city)
units           -- Căn hộ (floor, direction_main, direction_balcony, price, feng_score_cache)
conversations   -- Phiên chat (user_id, unit_id)
messages        -- Tin nhắn + AI pipeline data (rule_result, rag_chunks)
knowledge_chunks-- RAG vectors (content, embedding, source_doc) — pgvector
leads           -- Lead management (user_id, unit_id, sales_id, status)
```

---

## Cài đặt & Chạy local

### Yêu cầu
- Node.js 18+
- Python 3.11+
- Supabase account (free tier)
- Anthropic API key
- OpenAI API key (embeddings)

### Frontend
```bash
cd frontend
npm install
cp .env.example .env.local
# Điền NEXT_PUBLIC_SUPABASE_URL, NEXT_PUBLIC_SUPABASE_ANON_KEY
npm run dev
```

### Backend
```bash
cd backend
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
cp .env.example .env
# Điền ANTHROPIC_API_KEY, OPENAI_API_KEY, DATABASE_URL
uvicorn main:app --reload
```

### Supabase Setup
```sql
-- Enable pgvector
CREATE EXTENSION IF NOT EXISTS vector;

-- Run migrations
psql -f supabase/migrations/001_init.sql
```

---

## Environment Variables

### Frontend (.env.local)
```
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
NEXT_PUBLIC_API_URL=http://localhost:8000
```

### Backend (.env)
```
ANTHROPIC_API_KEY=
OPENAI_API_KEY=
DATABASE_URL=postgresql://...
SUPABASE_SERVICE_KEY=
RESEND_API_KEY=
```

---

## Project Structure

```
mindfeng/
├── frontend/               # Next.js 14
│   ├── app/
│   │   ├── (auth)/         # Login, Register, Onboarding
│   │   ├── projects/       # Danh sách dự án + căn hộ
│   │   ├── chat/           # AI Chat interface
│   │   ├── sales/          # Sales dashboard
│   │   └── admin/          # Admin panel
│   └── components/
│       ├── FengBadge/      # Feng score badge
│       ├── ComparisonCard/ # AI comparison card
│       └── ChatWindow/     # Streaming chat UI
│
├── backend/                # FastAPI
│   ├── routers/
│   │   ├── auth.py
│   │   ├── units.py
│   │   ├── chat.py         # AI chat endpoint
│   │   ├── rag.py          # RAG upload + search
│   │   └── leads.py
│   ├── services/
│   │   ├── rule_engine.py  # Bát trạch logic
│   │   ├── rag_service.py  # Chunking + embedding
│   │   └── ai_service.py   # Claude integration
│   └── models/
│
├── supabase/
│   └── migrations/
│
└── docs/                   # Gate G1 deliverables
    ├── brief_1page.html
    ├── prd_full.html
    └── wireframe_ui_flow.html
```

---

## Success Metrics

| Chỉ số | Mục tiêu |
|--------|----------|
| Chat session completion rate | ≥ 70% |
| Profile wizard completion | ≥ 80% |
| Tỷ lệ đặt lịch sau chat | ≥ 20% |
| AI response latency (p95) | < 4s |
| Lead/tháng | ≥ 30 |
| Uptime | ≥ 99% |

---

## Roadmap

- **v1.0 MVP** — 4 tuần (Gate G1 → G2)
- **v1.1** — AI tính mệnh cả gia đình, Real-time chat sales↔khách
- **v2.0** — Tư vấn nội thất theo phong thủy, Multi-project marketplace

---

## License

MIT — Built for educational purposes. Phong thủy tư vấn mang tính tham khảo theo quan niệm truyền thống.

---

*MindFeng — Tư vấn phong thủy nhất quán, cá nhân hóa, 24/7.*
