# 🎨 UI/UX Design

## Overview

Notra's user interface is built with **Vue 3**, **Vuestic UI**, and **Pinia** for state management, providing a modern, responsive experience across all portals.

---

## 🏗️ Architecture

### Technology Stack

- **Vue 3** - Progressive JavaScript framework
- **Vuestic UI** - Admin and enterprise-grade Vue 3 UI component library
- **Pinia** - State management
- **Vite** - Build tool and dev server

### Application Structure

```
src/
├── pages/              # Page components
│   ├── Dashboard.vue
│   ├── BookSession.vue
│   ├── UploadDocument.vue
│   └── JoinVideo.vue
├── components/         # Reusable components
│   ├── SessionCard.vue
│   ├── DocumentViewer.vue
│   └── SignatureOverlay.vue
├── stores/            # Pinia state stores
│   ├── user.js
│   ├── bookings.js
│   └── documents.js
└── App.vue            # Root component
```

---

## 📱 Core User Flows

### 1. Subscriber Portal

**Dashboard**
- Upcoming sessions calendar view
- Recent documents
- Quick actions (Upload, Book, View)
- Notification center

**Upload Document Flow**
1. Drag & drop or file picker
2. Document preview
3. Pre-sign digital signature
4. Confirmation

**Book Session Flow**
1. Select date/time from ENP availability
2. Choose session type (REN/IEN)
3. Add notes/purpose
4. Payment (if required)
5. Calendar invite sent

**Video Session**
1. Join button (appears 5 min before)
2. Camera/mic check
3. Full-screen video interface
4. Document viewer sidebar
5. Signature overlay
6. Session completion confirmation

---

### 2. ENP Portal

**Dashboard**
- Today's sessions timeline
- Client queue
- Pending documents for review
- Activity log

**Session Management**
- Client details card
- Document annotation tools
- Real-time video interface
- Notarization seal interface
- Post-session notes

**Document Review**
- Split view (original + annotated)
- Signature verification
- Seal placement
- Submit to Supreme Court

---

### 3. Admin Portal

**User Management**
- ENP verification queue
- Subscriber list with filters
- Role assignment interface

**Compliance Dashboard**
- Active sessions monitor
- Document audit trail
- Court submission log
- System health metrics

---

## 🎨 Design System

### Color Palette

| Purpose | Color | Usage |
|---------|-------|-------|
| Primary | Navy Blue (#1a1a2e) | Main actions, headers |
| Secondary | Indigo (#4f46e5) | Links, highlights |
| Success | Green (#10b981) | Confirmations, completed |
| Warning | Amber (#f59e0b) | Alerts, pending items |
| Error | Red (#ef4444) | Errors, critical actions |

### Typography

- **Headings**: Inter, Poppins (sans-serif)
- **Body**: System font stack for optimal performance
- **Code/Technical**: Monospace

### Components

Built with Vuestic UI components:
- `va-button` - Primary actions
- `va-card` - Content containers
- `va-data-table` - Lists and tables
- `va-modal` - Dialogs and overlays
- `va-file-upload` - Document upload
- `va-date-picker` - Session booking

---

## 📐 Responsive Design

### Breakpoints

- **Mobile**: < 640px
- **Tablet**: 640px - 1024px
- **Desktop**: > 1024px

### Mobile-First Approach

- Stack layouts on mobile
- Progressive enhancement for larger screens
- Touch-friendly tap targets (minimum 44x44px)
- Simplified navigation on mobile

---

## ♿ Accessibility

- WCAG 2.1 AA compliance
- Keyboard navigation support
- Screen reader compatibility
- High contrast mode
- Focus indicators on all interactive elements

---

## 🔐 Security Considerations

- No sensitive data in client-side storage
- Session tokens in HTTP-only cookies
- CSP headers for XSS protection
- Input sanitization on all forms

---

## 🚀 Performance

- Lazy loading for routes
- Code splitting per portal
- Optimized images (WebP with fallbacks)
- Service worker for offline capability (optional)

---

## 📊 Key Metrics

- First Contentful Paint: < 1.5s
- Time to Interactive: < 3.5s
- Lighthouse Score: > 90

---

## 🔄 Future Enhancements

- [ ] Dark mode toggle
- [ ] Multi-language support (English, Filipino)
- [ ] Voice-guided navigation
- [ ] Offline document viewing
- [ ] Progressive Web App (PWA) features
- [ ] AI-powered document suggestions

---

## 📝 Notes

The current UI bundle provides a basic scaffold structure. Detailed mockups, wireframes, and interactive prototypes will be added as the design evolves.

For developers working on the frontend:
- Follow Vue 3 Composition API patterns
- Use Vuestic components for consistency
- Keep state management centralized in Pinia stores
- Write component tests for critical user flows
