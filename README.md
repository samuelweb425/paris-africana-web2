# Paris Africana International School - Website

A modern, professional, and mobile-responsive website for Paris Africana International School located in Mararaba, Nasarawa State, Nigeria.

## Features

### Website Pages

- **Home**: Hero banner, school statistics, why choose us section, testimonials, latest news
- **About Us**: School history, mission & vision, core values, proprietor's message, management team
- **Academics**: Academic programs (Crèche, Nursery, Primary), curriculum, facilities, teaching methodology, extracurricular activities
- **Admissions**: Admission process, requirements, online application form, FAQs
- **Gallery**: Photo and video galleries organized by category
- **News & Events**: Latest news, announcements, upcoming events, school calendar
- **Parent Resources**: Downloadable documents, parenting tips, school policies, academic resources
- **Contact**: Contact information, inquiry form, Google Maps integration
- **Admin Dashboard**: Manage admissions and contact inquiries

### Technical Features

- **Database**: PostgreSQL with Drizzle ORM for data persistence
- **Forms & Submissions**: Online admission forms and contact inquiries stored in database
- **Responsive Design**: Mobile-first approach with Tailwind CSS
- **Brand Colors**: Royal Blue (#003d82), White, and Gold (#d4af37) accents
- **SEO Optimization**: Sitemap, robots.txt, meta tags, schema markup
- **WhatsApp Integration**: Floating WhatsApp button for quick communication
- **Social Media Links**: Integration with major social platforms

## Tech Stack

- **Frontend**: Next.js 16 (App Router), React 19, Tailwind CSS 4
- **Backend**: Next.js API Routes, Node.js
- **Database**: PostgreSQL with Drizzle ORM
- **Icons**: React Icons
- **Animations**: CSS animations, Framer Motion support

## Getting Started

### Prerequisites

- Node.js 18+ and npm
- PostgreSQL database
- Environment variables configured

### Installation

1. Clone the repository:
```bash
git clone https://github.com/yourusername/paris-africana.git
cd paris-africana
```

2. Install dependencies:
```bash
npm install
```

3. Set up environment variables (create `.env.local`):
```env
DATABASE_URL=postgresql://user:password@localhost:5432/parisafricana_db
```

4. Push database schema:
```bash
npx drizzle-kit push
```

5. Run development server:
```bash
npm run dev
```

Visit `http://localhost:3000` to see the website.

### Building for Production

```bash
npm run build
npm run start
```

## Project Structure

```
src/
├── app/                    # Next.js App Router pages
│   ├── page.tsx           # Home page
│   ├── about/             # About page
│   ├── academics/         # Academics page
│   ├── admissions/        # Admissions page
│   ├── gallery/           # Gallery page
│   ├── news/              # News & Events page
│   ├── resources/         # Parent Resources page
│   ├── contact/           # Contact page
│   ├── admin/             # Admin dashboard
│   ├── api/               # API routes
│   │   ├── admissions/    # Admissions API
│   │   ├── contacts/      # Contact form API
│   │   └── health/        # Health check endpoint
│   ├── layout.tsx         # Root layout
│   ├── globals.css        # Global styles
│   └── sitemap.ts         # SEO sitemap
├── components/            # Reusable React components
│   ├── Header.tsx         # Navigation header
│   ├── Footer.tsx         # Footer
│   ├── Hero.tsx           # Hero banner component
│   ├── WhatsAppButton.tsx # WhatsApp CTA
│   ├── AdmissionForm.tsx  # Admission form
│   └── ContactForm.tsx    # Contact form
├── db/
│   ├── index.ts           # Database connection
│   └── schema.ts          # Drizzle ORM schema
└── lib/                   # Utilities and helpers
```

## Database Schema

### Tables

1. **admissionsTable**: Student admission applications
   - Child information, parent details, class level, status

2. **contactsTable**: Contact form submissions
   - Name, email, subject, message, status

3. **tuitionInquiriesTable**: Tuition fee inquiries
   - Contact info, class level, inquiry type

4. **newsletterTable**: Newsletter subscriptions
   - Email addresses of subscribers

5. **newsTable**: News articles and announcements
   - Title, content, featured image, publication status

6. **eventsTable**: School events
   - Event name, dates, location, featured status

7. **galleryTable**: Photo gallery items
   - Image URL, category, description

8. **testimonialsTable**: Parent testimonials
   - Parent name, child name, rating, content

9. **adminUsersTable**: Admin user accounts
   - Email, password hash, role, login history

10. **settingsTable**: Site configuration
    - Key-value pairs for settings

## API Endpoints

### Admissions
- `POST /api/admissions`: Submit admission application
- `GET /api/admissions`: Retrieve all admissions (requires authentication)

### Contacts
- `POST /api/contacts`: Submit contact form
- `GET /api/contacts`: Retrieve all contacts (requires authentication)

### Health
- `GET /api/health`: Health check endpoint

## Customization

### Color Scheme

The brand colors are defined in `src/app/globals.css`:
- Primary Blue: `#003d82`
- Secondary Blue: `#1e5ba8`
- Gold Accent: `#d4af37`

To change colors, update the CSS variables in the `:root` selector.

### Contact Information

Update the school contact details in:
- `src/components/Header.tsx`
- `src/components/Footer.tsx`
- `src/app/contact/page.tsx`

Current contact info:
- **Phone**: +234 814 666 7644
- **Address**: Mararaba, Nasarawa State, Nigeria
- **Email**: info@parisafricana.com
- **WhatsApp**: https://wa.me/2348146667644

### Adding News Articles

News articles can be added through:
1. Database directly via Drizzle
2. Admin dashboard (future implementation with CMS)

### Extending the Database

To add new tables:
1. Define the table in `src/db/schema.ts`
2. Run `npx drizzle-kit push` to apply changes
3. Create corresponding API routes

## SEO Optimization

The website includes:
- Meta tags and descriptions for all pages
- Open Graph tags for social sharing
- Sitemap generation
- robots.txt configuration
- Structured data markup
- Mobile-friendly design
- Fast page load times

### Targeting Keywords

The website is optimized for:
- Best Nursery School in Mararaba
- Best Primary School in Nasarawa State
- International School in Mararaba
- Nursery and Primary School in Nigeria

## Security Considerations

- Admin panel uses basic token authentication (enhance in production)
- Database queries use parameterized statements
- CORS protection on API routes
- Environment variables for sensitive data
- Input validation on forms

### Production Deployment

For production:
1. Use environment variables for all secrets
2. Implement proper JWT authentication
3. Enable HTTPS
4. Set up database backups
5. Configure CDN for static assets
6. Implement rate limiting on API routes
7. Set up monitoring and logging

## Performance

- Static page generation where possible
- Image optimization with Next.js Image component
- CSS minification via Tailwind CSS
- Lazy loading for images
- Responsive design for all devices

## Browser Support

- Chrome/Edge: Latest 2 versions
- Firefox: Latest 2 versions
- Safari: Latest 2 versions
- Mobile: All modern mobile browsers

## Contributing

For contributions, please:
1. Create a feature branch
2. Make your changes
3. Submit a pull request
4. Ensure all tests pass

## License

This project is proprietary to Paris Africana International School.

## Support

For technical support or inquiries:
- Email: info@parisafricana.com
- Phone: +234 814 666 7644
- WhatsApp: https://wa.me/2348146667644

## Deployment

### Vercel (Recommended)

```bash
npm install -g vercel
vercel
```

### Docker

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY . .
RUN npm install && npm run build
EXPOSE 3000
CMD ["npm", "start"]
```

## Future Enhancements

- [ ] Student portal for grades and attendance
- [ ] Payment gateway integration for fees
- [ ] Online class scheduling
- [ ] Mobile app (iOS/Android)
- [ ] Live chat support
- [ ] Email notifications
- [ ] Advanced analytics
- [ ] Multi-language support

---

**Last Updated**: 2024
**Version**: 1.0.0
