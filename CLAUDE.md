# CLAUDE.md - AI Assistant Guide for BeyazPro

## Project Overview

**BeyazPro** is a Turkish-to-English vocabulary learning web application with pronunciation practice and interactive games. The application is built as a single-file static HTML application with embedded CSS and JavaScript.

**Target Users:** Turkish speakers learning English vocabulary

**Primary Language:** Turkish (UI) / English (vocabulary content)

## Technology Stack

| Component | Technology |
|-----------|------------|
| Frontend | Vanilla HTML5, CSS3, JavaScript (ES6+) |
| Backend | Supabase (BaaS - Database & Authentication) |
| Audio | Web Speech Recognition API, Web Speech Synthesis API |
| Storage | Supabase Database + localStorage fallback |
| CDN | Supabase JS SDK v2.38.4 |

## Project Structure

```
BeyazPro/
├── index.html          # Complete application (CSS + HTML + JS in one file)
├── .env.example.txt    # Environment variables template
├── .gitignore.txt      # Git ignore rules
├── README.md           # Brief project description
└── CLAUDE.md           # This file
```

### File Organization within index.html

| Section | Lines | Description |
|---------|-------|-------------|
| CSS Styles | 8-734 | All styling including animations |
| HTML Markup | 736-995 | Application structure and UI |
| JavaScript | 997-1841 | All application logic |

## Coding Conventions

### Naming Conventions

- **Variables:** camelCase (e.g., `currentUser`, `learnPool`, `micReady`)
- **Functions:** camelCase (e.g., `handleAuth()`, `startQuiz()`, `notif()`)
- **HTML IDs:** kebab-case (e.g., `btn-learn`, `quiz-memory-filter`, `auth-status`)
- **CSS Classes:** kebab-case (e.g., `.auth-panel`, `.stat-box`, `.word-card`)

### CSS Variables (Theme Colors)

```css
:root {
    --primary: #667eea;      /* Purple - main brand color */
    --primary-dark: #764ba2; /* Darker purple */
    --success: #4facfe;      /* Blue - success states */
    --warning: #ffa500;      /* Orange - warnings */
    --danger: #f5576c;       /* Red - errors/danger */
    --secondary: #f093fb;    /* Pink - secondary elements */
    --dark: #1f2125;         /* Dark gray - text */
    --light: #f5f5f5;        /* Light gray - backgrounds */
}
```

### Data Structures

**Word Object Schema:**
```javascript
{
    english: string,      // English word (required)
    phonetic: string,     // IPA phonetic notation
    turkish: string,      // Turkish translation (required)
    memory: string,       // 'learning' | 'almost' | 'completed' | 'difficult'
    count: number,        // Study count (currently unused)
    type: string,         // Word category (noun, verb, etc.)
    repetitions: number   // Repetition tracking (currently unused)
}
```

**Global State Variables:**
```javascript
vocab[]         // Array of all word objects
idx             // Current word index
qPool[]         // Quiz word pool
mPool[]         // Matching game pool
selectedWords   // Set of selected word indices
learnPool[]     // Randomized learning pool
currentUser     // Authenticated Supabase user
micReady        // Boolean - microphone initialized
```

## Key Features & Modes

1. **Learn Mode** (`📖`) - Sequential flashcard-style word learning
2. **Quiz Mode** (`❓`) - Multiple choice English-to-Turkish tests
3. **Matching Mode** (`🎮`) - Word matching game
4. **Management Mode** (`📊`) - Add, edit, delete, import/export words

### Memory States
- `learning` - New/learning words
- `almost` - Almost memorized
- `completed` - Fully memorized
- `difficult` - Marked as difficult

## Development Workflow

### Running Locally

```bash
# Option 1: Python
python -m http.server 8000

# Option 2: Node.js
npx http-server

# Option 3: PHP
php -S localhost:8000
```

Then open `http://localhost:8000` in browser.

### Making Changes

1. Edit `index.html` directly
2. CSS changes: Modify the `<style>` section (lines 8-734)
3. JavaScript changes: Modify the `<script>` section (lines 997-1841)
4. HTML changes: Modify the markup (lines 736-995)
5. Test in browser (refresh to see changes)
6. Commit and push

### No Build Step Required

This is a static HTML application - no bundling, transpilation, or build process needed.

## Browser APIs Used

| API | Purpose | Browser Support |
|-----|---------|-----------------|
| Web Speech Recognition | Voice input for pronunciation | Chrome, Edge |
| Web Speech Synthesis | Text-to-speech for words | All modern browsers |
| MediaDevices | Microphone access | All modern browsers |
| localStorage | Offline data persistence | All browsers |
| FileReader | CSV file import | All modern browsers |

## Supabase Integration

### Database Table: `words`
- User vocabulary is stored per-user with `user_id` column
- CRUD operations via Supabase JS SDK

### Authentication
- Email/password authentication
- Automatic session management
- Fallback to localStorage when offline

## Important Implementation Notes

### Error Handling
- User-facing errors shown via `notif(message, type)` function
- Types: `'success'`, `'error'`, `'info'`

### Responsive Design
- Mobile breakpoint: 768px
- Uses CSS Grid and Flexbox
- Touch-friendly button sizes

### Animations
- CSS keyframe animations defined at top of styles
- `slideInDown`, `slideInUp`, `fadeIn`, `scaleIn`, `bounceIn`, `glow`

## Security Considerations

**Current Issues (for awareness):**
- Supabase credentials are visible in client-side code
- Using anon key with Row Level Security (RLS) assumed
- No server-side validation

**When making changes:**
- Never expose additional API keys or secrets
- Validate user input on client side
- Be aware this is a client-side only application

## Common Tasks

### Adding a New Word Programmatically
```javascript
vocab.push({
    english: "word",
    phonetic: "wɜːrd",
    turkish: "kelime",
    memory: "learning",
    count: 0,
    type: "noun",
    repetitions: 0
});
saveWordsToSupabase(); // or saveToLocalStorage()
```

### Showing a Notification
```javascript
notif('Message here', 'success'); // or 'error', 'info'
```

### Changing Modes
```javascript
showMode('learn');  // or 'quiz', 'match', 'manage'
```

## Testing

No automated tests exist. Manual testing checklist:
1. Authentication flow (login/register/logout)
2. All four modes work correctly
3. Word CRUD operations
4. Import/Export CSV
5. Speech recognition and synthesis
6. Responsive design on mobile

## Known Limitations

1. Single-file architecture limits modularity
2. `count` and `repetitions` fields are defined but unused
3. No offline-first sync strategy
4. Limited accessibility (missing ARIA labels)
5. Web Speech API has limited browser support

## Quick Reference

### Key DOM Element IDs
- `#btn-learn`, `#btn-quiz`, `#btn-match`, `#btn-manage` - Mode buttons
- `#word-display` - Main word card display
- `#auth-status` - Login status indicator
- `#vocab-list` - Word management table

### Key Functions
- `initSupabase()` - Initialize Supabase connection
- `handleAuth()` - Login/register handler
- `startQuiz()` - Begin quiz mode
- `startMatch()` - Begin matching game
- `showMode(mode)` - Switch between modes
- `renderVocabList()` - Refresh word list UI
- `saveWordsToSupabase()` - Persist to cloud
- `saveToLocalStorage()` - Persist locally

## Git Workflow

- Main development happens on feature branches
- Commit messages should be descriptive
- Push to designated branch before creating PR
