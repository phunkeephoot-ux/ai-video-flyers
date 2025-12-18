# AI Video Flyers - Deployment & Testing Guide

## 🚀 Production Deployment Checklist

### Pre-Deployment
- [ ] All environment variables configured
- [ ] Database migrations run
- [ ] Stripe production keys obtained
- [ ] Coinbase Commerce account setup
- [ ] AWS S3 bucket created
- [ ] Domain/DNS configured
- [ ] SSL certificate provisioned
- [ ] Email service (Resend) configured

### Vercel Deployment

1. **Link Repository**
   ```bash
   npm i -g vercel
   vercel link
   ```

2. **Set Environment Variables**
   ```bash
   vercel env add DATABASE_URL
   vercel env add NEXTAUTH_SECRET
   vercel env add STRIPE_SECRET_KEY
   vercel env add STRIPE_PUBLISHABLE_KEY
   vercel env add COINBASE_API_KEY
   # ... add all other variables
   ```

3. **Deploy to Production**
   ```bash
   vercel deploy --prod
   ```

### Docker Deployment

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build
EXPOSE 3000
CMD ["npm", "start"]
```

```bash
docker build -t ai-video-flyers .
docker run -p 3000:3000 --env-file .env.production ai-video-flyers
```

### AWS EC2/ECS Deployment

1. **Create Dockerfile** (see above)
2. **Push to ECR**
   ```bash
   aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ACCOUNT.dkr.ecr.us-east-1.amazonaws.com
   docker tag ai-video-flyers:latest ACCOUNT.dkr.ecr.us-east-1.amazonaws.com/ai-video-flyers:latest
   docker push ACCOUNT.dkr.ecr.us-east-1.amazonaws.com/ai-video-flyers:latest
   ```
3. **Configure ECS Task**
4. **Set up Load Balancer**
5. **Configure Auto-scaling**

## 🧪 Testing Guide

### Unit Tests

```bash
npm install --save-dev jest @testing-library/react @testing-library/jest-dom
npm test
```

### E2E Testing

```bash
npm install --save-dev playwright @playwright/test
npx playwright test
```

### Payment Testing

#### Stripe Test Cards
- Success: `4242 4242 4242 4242`
- Decline: `4000 0000 0000 0002`
- Authentication Required: `4000 0025 0000 3155`

#### Coinbase Commerce
- Use Sandbox API keys for testing
- Mock webhook events locally

### Video Generation Testing

1. **Mock API Responses**
   ```typescript
   // lib/__mocks__/video-api.ts
   export const generateVideo = jest.fn(() => 
     Promise.resolve({id: 'mock-123', status: 'completed'})
   );
   ```

2. **Test Upload Process**
   ```bash
   npm run test:uploads
   ```

## 📊 Performance Optimization

### Frontend
- Use Next.js Image optimization
- Implement lazy loading
- Minimize bundle size
- Enable compression

```javascript
// next.config.js
module.exports = {
  compress: true,
  swcMinify: true,
  images: {
    domains: ['cdn.yourdomain.com'],
    sizes: [320, 640, 1280],
  },
};
```

### Backend
- Enable API caching
- Use database indexes
- Implement rate limiting
- Queue long-running jobs

### Monitoring

```javascript
// sentry integration
import * as Sentry from "@sentry/nextjs";

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  environment: process.env.NODE_ENV,
});
```

## 🔒 Security Hardening

### CORS Configuration
```javascript
// lib/cors.ts
const corsOptions = {
  origin: process.env.NEXT_PUBLIC_APP_URL,
  methods: ['GET', 'POST', 'PUT', 'DELETE'],
  credentials: true,
};
```

### Rate Limiting
```bash
npm install express-rate-limit
```

### HTTPS Enforcement
```javascript
// middleware.ts
export function middleware(request) {
  if (request.headers['x-forwarded-proto'] !== 'https') {
    return NextResponse.redirect(
      `https://${request.headers['host']}${request.nextUrl.pathname}`,
      301
    );
  }
}
```

### Database Security
- Use VPC for database
- Enable encryption at rest
- Regular backups
- Connection pooling

## 📈 Scaling Strategy

### Horizontal Scaling
- Use load balancer
- Multiple app instances
- Separate database from app tier
- Use CDN for static assets

### Database Scaling
- Read replicas for analytics
- Connection pooling (PgBouncer)
- Partitioning for large tables
- Archive old data

### File Storage Scaling
- Use S3 with CloudFront CDN
- Enable versioning
- Lifecycle policies for old videos
- Cross-region replication

## 📝 Logging & Monitoring

### Logging
```bash
npm install pino pino-pretty
```

### Monitoring
- Vercel Analytics
- CloudWatch (AWS)
- Datadog
- New Relic

### Key Metrics
- API response time
- Payment success rate
- Video generation time
- Error rate
- Video delivery rate

## 💰 Cost Optimization

### Stripe
- 2.9% + 30¢ per transaction
- Optimize transaction volume

### Coinbase Commerce
- Free tier available
- Settle to USD/USDC

### AWS S3
- Use S3 Standard for frequent access
- Archive to Glacier after 30 days
- Use CloudFront CDN (caching)

### Video API
- RunwayML: $10/month free tier
- Creatomate: Pay per video
- Consider batch processing

### Hosting
- Vercel: $20/month pro
- AWS: ~$100-500/month depending on scale
- Docker: ~$50-200/month on DigitalOcean/Linode

## 🎯 Post-Launch

### Day 1
- Monitor error logs
- Check payment webhook events
- Verify email delivery
- Test order fulfillment

### Week 1
- Analyze user behavior
- Optimize slow endpoints
- Fix reported bugs
- Gather user feedback

### Month 1
- Review analytics
- Optimize conversion funnel
- Improve video quality
- Plan feature enhancements

## 📞 Support & Maintenance

### Monitoring Tasks
- Daily log review
- Weekly performance report
- Monthly security audit
- Quarterly cost review

### Update Schedule
- Security patches: immediate
- Bug fixes: weekly
- Feature releases: monthly
- Major updates: quarterly

---

**Last Updated:** December 2025
