# AI Video Flyers - Full-Stack Application

> AI-powered video flyer generator with Stripe & Crypto payments. Users purchase 0-45s ($75) or 45s-90s ($150) video flyers, upload images & music, specify creative preferences, and receive AI-generated videos.

## 🚀 Features

- **Dual Pricing Model**: 0-45 seconds ($75) & 45-90 seconds ($150)
- **Multi-Format Upload**: Support for PNG, JPG, MP3, M4A, WAV, AU
- **AI Video Generation**: Automated video creation using RunwayML or Creatomate API
- **Omni-Payment Support**: 
  - Stripe (credit/debit cards, Apple Pay, Google Pay)
  - Coinbase Commerce (Bitcoin, Ethereum, USDC, USDT, DAI, etc.)
- **Creative Brief System**: Theme, color scheme, mood, character style, dimensions
- **Admin Dashboard**: Order management, status tracking, video delivery
- **Email Notifications**: Automated delivery and updates
- **One-Click Revision**: Simple revision request workflow

## 🛠️ Tech Stack

- **Frontend**: React 18+, Next.js 14, TypeScript, Tailwind CSS, ShadCN UI
- **Backend**: Next.js API Routes, Prisma ORM, PostgreSQL
- **Payments**: Stripe SDK, Coinbase Commerce API
- **Video**: RunwayML API or Creatomate API
- **Storage**: AWS S3 or Vercel Blob
- **Auth**: NextAuth.js with email/OAuth
- **Email**: Resend or SendGrid
- **Deployment**: Vercel, Docker, AWS

## 📋 Prerequisites

- Node.js 18.17+
- npm or yarn
- PostgreSQL 13+
- AWS S3 account (or Vercel Blob)
- Stripe account
- Coinbase Commerce account
- RunwayML or Creatomate account
- Resend or SendGrid account

## 🚀 Quick Start

### 1. Clone Repository
```bash
git clone https://github.com/yourusername/ai-video-flyers.git
cd ai-video-flyers
npm install
```

### 2. Environment Setup
Create `.env.local`:
```env
# Database
DATABASE_URL="postgresql://user:password@localhost:5432/ai_video_flyers"

# Authentication
NEXTAUTH_SECRET="your-secret-key"
NEXTAUTH_URL="http://localhost:3000"

# Stripe
STRIPE_SECRET_KEY="sk_test_..."
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY="pk_test_..."
STRIPE_WEBHOOK_SECRET="whsec_..."

# Coinbase Commerce
COINBASE_API_KEY="your-coinbase-api-key"
COINBASE_WEBHOOK_SECRET="your-webhook-secret"

# AWS S3
AWS_REGION="us-east-1"
AWS_ACCESS_KEY_ID="your-access-key"
AWS_SECRET_ACCESS_KEY="your-secret-key"
AWS_S3_BUCKET="your-bucket-name"

# Video Generation (RunwayML)
RUNWAYML_API_KEY="your-runwayml-key"

# OR Video Generation (Creatomate)
CREATOMAPTE_API_KEY="your-creatomate-key"

# Email
RESEND_API_KEY="your-resend-key"
FROM_EMAIL="noreply@yourdomain.com"

# App Config
NEXT_PUBLIC_APP_URL="https://yourdomain.com"
NODE_ENV="development"
```

### 3. Database Setup
```bash
npm run db:migrate
npm run db:studio  # View database in Prisma Studio
```

### 4. Start Development Server
```bash
npm run dev
# Open http://localhost:3000
```

## 📁 Project Structure

```
src/
├── app/
│   ├── api/
│   │   ├── auth/                 # NextAuth routes
│   │   ├── orders/               # Order CRUD operations
│   │   ├── payments/             # Payment webhooks
│   │   ├── uploads/              # File upload handlers
│   │   ├── videos/               # Video generation jobs
│   │   └── admin/                # Admin operations
│   ├── dashboard/                # User dashboard page
│   ├── admin/                    # Admin dashboard
│   ├── order/[id]/               # Order details page
│   ├── layout.tsx                # Root layout
│   └── page.tsx                  # Landing page
├── components/
│   ├── landing/                  # Landing page components
│   ├── pricing/                  # Pricing cards
│   ├── brief-form/               # Creative brief form
│   ├── checkout/                 # Payment checkout
│   ├── admin/                    # Admin components
│   └── ui/                       # ShadCN UI components
├── lib/
│   ├── db.ts                     # Prisma client
│   ├── stripe.ts                 # Stripe utilities
│   ├── coinbase.ts               # Coinbase Commerce utilities
│   ├── video-api.ts              # Video generation API
│   ├── s3.ts                     # AWS S3 utilities
│   ├── email.ts                  # Email service
│   └── auth.ts                   # NextAuth config
├── types/
│   └── index.ts                  # TypeScript definitions
├── prisma/
│   └── schema.prisma             # Database schema
└── styles/
    └── globals.css               # Global styles
```

## 🔌 API Endpoints

### Orders
- `POST /api/orders` - Create new order
- `GET /api/orders` - List user's orders
- `GET /api/orders/[id]` - Get order details
- `PATCH /api/orders/[id]` - Update order
- `DELETE /api/orders/[id]` - Delete order

### Payments
- `POST /api/payments/stripe` - Create Stripe checkout
- `POST /api/payments/coinbase` - Create Coinbase Commerce charge
- `POST /webhooks/stripe` - Stripe payment webhook
- `POST /webhooks/coinbase` - Coinbase webhook

### Videos
- `POST /api/videos/generate` - Trigger video generation
- `GET /api/videos/[id]/status` - Check generation status
- `GET /api/videos/[id]/download` - Download completed video

### Admin
- `GET /api/admin/orders` - List all orders
- `GET /api/admin/stats` - Dashboard statistics
- `PATCH /api/admin/orders/[id]/status` - Update order status

## 🎬 Video Generation Workflow

1. **User Submits Brief**
   - Uploads images (PNG/JPG)
   - Uploads audio (MP3/M4A/WAV/AU)
   - Specifies theme, colors, mood, style, dimensions
   - Reviews and confirms

2. **Payment Processing**
   - User selects duration tier ($75 or $150)
   - Chooses payment method (card/wallet/crypto)
   - Payment gateway processes transaction
   - Webhook confirms payment

3. **Video Generation**
   - Assets uploaded to S3
   - Job queued in Prisma
   - Background job calls video API
   - Renders video with parameters:
     - Duration (45s or 90s max)
     - User's images placed in template
     - Brand colors applied
     - Mood/theme affects transitions
     - Audio track synced
     - Output dimensions (9:16, 1:1, 16:9)

4. **Delivery**
   - Video stored in S3
   - Email sent with download link
   - User views in dashboard
   - One free revision included

## 💳 Payment Configuration

### Stripe Setup
```bash
# Create products in Stripe Dashboard:
# 1. "Video Flyer - Up to 45s" - $75
# 2. "Video Flyer - 45s to 90s" - $150

# Create webhook endpoints:
# - https://yourdomain.com/api/webhooks/stripe
# Events: payment_intent.succeeded, charge.refunded
```

### Coinbase Commerce Setup
```bash
# Create products in Coinbase:
# 1. "Video Flyer - Up to 45s" - $75
# 2. "Video Flyer - 45s to 90s" - $150

# Create webhook:
# - https://yourdomain.com/api/webhooks/coinbase
# Events: charge:confirmed
```

## 🧪 Testing

### Stripe Test Cards
- Success: 4242 4242 4242 4242
- Decline: 4000 0000 0000 0002

### Coinbase Commerce
- Testnet available in Sandbox mode

## 📱 Mobile App (Future)

This repository is set up for future React Native mobile app:
```bash
mkdir apps/mobile
cd apps/mobile
expo init --template
```

## 🚢 Deployment

### Vercel (Recommended)
```bash
# Link repository
npm i -g vercel
vercel link

# Set environment variables in Vercel dashboard
vercel env pull

# Deploy
vercel deploy --prod
```

### Docker
```bash
docker build -t ai-video-flyers .
docker run -p 3000:3000 ai-video-flyers
```

### Environment for Production
- Use production API keys (not test keys)
- Enable HTTPS
- Configure CORS properly
- Set up database backups
- Monitor logs and errors
- Set up CDN for video delivery

## 📊 Monitoring & Analytics

- Vercel Analytics for performance
- Stripe Dashboard for payments
- Coinbase Dashboard for crypto payments
- CloudWatch for AWS S3 usage
- Email delivery tracking via Resend

## 🔐 Security Checklist

- [ ] All secrets in environment variables
- [ ] HTTPS enabled
- [ ] CORS configured correctly
- [ ] Webhook signatures verified
- [ ] Rate limiting enabled
- [ ] File upload validation
- [ ] SQL injection prevention (Prisma)
- [ ] XSS protection (React + Next.js)
- [ ] CSRF tokens on forms
- [ ] Regular security audits

## 🐛 Troubleshooting

### Video Generation Fails
- Check RunwayML/Creatomate API key
- Verify S3 bucket permissions
- Check file upload limits
- Review API quotas

### Payment Issues
- Verify Stripe/Coinbase API keys
- Check webhook endpoints responding
- Review webhook logs
- Confirm webhook signatures

### Database Connection Issues
- Verify DATABASE_URL format
- Check PostgreSQL is running
- Ensure database exists
- Run migrations

## 📈 Future Enhancements

- [ ] Advanced video templates
- [ ] Real-time preview
- [ ] Batch video generation
- [ ] Video analytics
- [ ] Custom branding
- [ ] Team collaboration
- [ ] API for third-party integrations
- [ ] Mobile app (iOS/Android)
- [ ] AI script generation
- [ ] Subtitle/caption support

## 📄 License

MIT License - see LICENSE file

## 🤝 Contributing

Contributions welcome! Please:
1. Fork repository
2. Create feature branch
3. Commit changes
4. Push to branch
5. Open Pull Request

## 📞 Support

- Email: support@yourdomain.com
- Documentation: /docs
- Issues: GitHub Issues

---

**Built with ❤️ for creators**
