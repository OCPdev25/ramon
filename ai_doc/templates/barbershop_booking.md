# Barbershop Booking Site Template

## Overview
This template provides a structured approach for building an SEO-optimized barbershop website with integrated Square booking functionality.

## Architecture Pattern

### Static vs Dynamic Strategy
- **Static Pages** (Astro SSG):
  - Homepage with hero, services overview
  - About page with barber profiles  
  - Services & pricing page
  - Gallery/portfolio page
  - Contact & location page

- **Dynamic Features** (API Routes + Client-Side):
  - Booking widget (Square SDK)
  - Real-time availability checker
  - Booking confirmation flow
  - Customer account area

### Technology Stack
```
Frontend:
- Astro (Static Site Generation)
- Tailwind CSS v4 (Styling)
- Alpine.js or Svelte (Interactive components)

Backend/APIs:
- Astro API routes for Square integration
- Square Booking API
- Square Payments API

SEO/Marketing:
- JSON-LD structured data
- Local business schema
- Google Business Profile integration
- Sitemap & robots.txt
```

## Key Features Implementation

### 1. SEO Optimization
- Local SEO focus with location-based keywords
- Schema markup for local business
- Fast Core Web Vitals with static generation
- Mobile-first responsive design

### 2. Booking System Integration
```javascript
// Example Square booking flow
1. Customer selects service
2. Check availability via Square API
3. Present time slots
4. Collect customer info
5. Create booking via API
6. Process payment if required
7. Send confirmation
```

### 3. Essential Pages Structure

**Homepage**
- Hero with CTA to book
- Featured services
- Testimonials
- Location & hours
- Instagram feed integration

**Services Page**
- Service categories (Haircuts, Beard, Special)
- Pricing table
- Duration estimates
- "Book Now" CTAs

**Booking Page**
- Service selection
- Barber selection (optional)
- Calendar with availability
- Customer information form
- Confirmation step

### 4. Common Challenges & Solutions

**Challenge**: SSG vs Dynamic Booking
**Solution**: Hybrid approach - static shell with dynamic island for booking widget

**Challenge**: SEO vs SPA behavior  
**Solution**: Progressive enhancement - full page loads for SEO, smooth transitions for UX

**Challenge**: Square API rate limits
**Solution**: Implement caching layer for availability checks

**Challenge**: Mobile booking experience
**Solution**: Simplified mobile flow with fewer steps

## Implementation Checklist

### Phase 1: Foundation (Week 1-2)
- [ ] Set up Astro project with Tailwind v4
- [ ] Create page layouts and components
- [ ] Implement responsive navigation
- [ ] Set up SEO foundations (meta, schema)

### Phase 2: Content & Design (Week 2-3)
- [ ] Design and build static pages
- [ ] Integrate brand assets and imagery
- [ ] Implement gallery/portfolio
- [ ] Add contact forms

### Phase 3: Booking Integration (Week 3-4)
- [ ] Set up Square SDK
- [ ] Create booking flow UI
- [ ] Implement availability checking
- [ ] Test booking creation

### Phase 4: Optimization (Week 5)
- [ ] Performance optimization
- [ ] SEO fine-tuning
- [ ] Mobile experience polish
- [ ] Cross-browser testing

### Phase 5: Launch Prep (Week 6)
- [ ] Analytics setup
- [ ] Error tracking
- [ ] Deployment configuration
- [ ] Client training

## Square Integration Code Example

```typescript
// api/check-availability.ts
import { SquareClient } from 'square';

export async function GET({ request }) {
  const url = new URL(request.url);
  const date = url.searchParams.get('date');
  const serviceId = url.searchParams.get('serviceId');
  
  const client = new SquareClient({
    accessToken: import.meta.env.SQUARE_ACCESS_TOKEN,
  });
  
  try {
    const response = await client.bookingsApi.searchAvailability({
      query: {
        filter: {
          startAtRange: {
            startAt: date,
            endAt: // next day
          },
          serviceVariationId: serviceId,
        }
      }
    });
    
    return new Response(JSON.stringify(response.result), {
      headers: { 'Content-Type': 'application/json' }
    });
  } catch (error) {
    return new Response(JSON.stringify({ error: error.message }), {
      status: 500,
      headers: { 'Content-Type': 'application/json' }
    });
  }
}
```

## Notes
- Always prioritize mobile experience for barbershop sites
- Local SEO is critical - ensure NAP (Name, Address, Phone) consistency
- Consider walk-in vs appointment balance in UX
- Integration with existing Square POS is key for inventory/staff management