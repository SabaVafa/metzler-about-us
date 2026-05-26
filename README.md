# Metzler — About Us page

HTML/CSS prototype of the redesigned "Über uns" page for [edelstahl-tuerklingel.de](https://edelstahl-tuerklingel.de). Built as a standalone preview so the layout, copy, and component rhythm can be validated before integration into the production CMS.

## Preview

Open `index.html` in a browser. No build step, no dependencies.

## Structure

```
index.html              Markup (header → hero → ntv carousel → pillars → sortiment → bespoke → sustainability → locations → proof → closing CTA → footer)
style.css               About-page-specific styles (loaded AFTER styles-v2.css)
styles-v2.css           Shared design system from the homepage project (tokens, .btn, .container, .header, .footer, .hero-banner)

Banner/                 Hero photography (image 1.png … image 13.png — iterations from the design process)
Carousel Images/NTV V2/ ntv ceremony photos for the award carousel
images/                 Product photography (Briefkasten, Sprechanlage, Türklingel, etc.)
Trust badges/           Award badges (ntv, red dot, TrustedShops, Vergleich.org, Top Shop years)
Awards/                 Ceremony shots
Logo/                   Metzler logo (SVG, red-on-white + red-on-black)
ICONS/                  UI icons (cart, profile, menu, search, star rating)
Paymentshipping logos/  DHL, DPD, GoGreen, payment methods (used in footer)
```

## Design system

The chrome (header, footer, topbar, mobile nav, button system, design tokens) is imported 1:1 from the homepage project via `styles-v2.css`. About-specific styles in `style.css` are namespaced under `.about` / `.about-*` so they never collide with shared classes.

Key tokens used:
- Petrol primary: `#006D75`
- Anthracite hero column: `#1F2226`
- Text ink: `#1A171B`
- Soft background: `#F5F6FA`
- Hairline border: `#33363A`

## Placeholders still to fill

- `[GESCHÄFTSFÜHRER NAME]` and signature image (Section 2, once relevant)
- Standorte 2–5 (Section 7) — city + function
- Sustainability mid-target year + milestone (Section 6, middle column)
- 3 customer quotes from TrustedShops (Section 8 testimonials)
- High-resolution Banner export (current is 2800 × 2100; consider re-cropping for the wider hero panel)

## Notes

- Made-in-Germany claims are scoped to the steel product line (`EDELSTAHL · MADE IN GERMANY`), not the catalogue overall — accurate against the partner-manufactured electronics.
- "Familienunternehmen" wording was removed across the page after confirming Metzler is not a family company.
- The ntv award is featured exclusively in the dedicated Story / award section (Section 2), not in the hero chips — avoids duplication.
