# CLAUDE.md - BeyazPro Codebase Guide

## Project Overview

**BeyazPro Pro** is a cloud-based English vocabulary learning application with interactive gamification features. The UI is in Turkish and targets Turkish speakers learning English words.

- **Type**: Single-Page Application (SPA)
- **Stack**: Vanilla HTML5 + CSS3 + JavaScript (ES6+)
- **Backend**: Supabase (Backend-as-a-Service)
- **Build System**: None - static HTML file served directly

## Quick Start

```bash
# No build required - serve the static HTML file directly
python -m http.server 8000
# or
npx live-server
```

Then open `http://localhost:8000` in your browser.

## Project Structure

```
/home/user/BeyazPro/
├── index.html           # Complete application (1,842 lines - HTML/CSS/JS combined)
├── README.md            # Brief project description
├── .env.example.txt     # Supabase credentials template
├── .gitignore.txt       # Git ignore patterns
└── CLAUDE.md            # This file
```

**Important**: This is a monolithic single-file application. All HTML, CSS (734 lines), and JavaScript (842 lines) are contained within `index.html`.

## Architecture

### File Structure within index.html

| Section | Lines | Description |
|---------|-------|-------------|
| HTML Structure | 1-996 | DOM elements, containers, modes |
| CSS Styles | 16-749 | Complete styling with CSS variables |
| JavaScript | 997-1839 | All application logic |

### Core Application Modes

1. **Learn Mode** (`#learn-mode`) - Default mode, displays words with pronunciation
2. **Quiz Mode** (`#quiz-mode`) - Multiple choice questions
3. **Match Mode** (`#match-mode`) - Pair matching game
4. **Manage Mode** (`#manage-mode`) - Word management, import/export, statistics

### Data Flow

```
User Action → JavaScript Handler → In-Memory vocab[] → Supabase/LocalStorage
```

- **Primary Storage**: Supabase PostgreSQL (requires authentication)
- **Fallback Storage**: Browser LocalStorage (key: `beyazpro_vocab`)

## Key Technologies

| Technology | Version | Purpose |
|------------|---------|---------|
| Supabase.js | 2.38.4 | Database, authentication |
| Web Speech API | Native | Text-to-speech, speech-to-text |
| LocalStorage | Native | Offline data persistence |
| FileReader API | Native | CSV import functionality |

## Data Model

### Vocabulary Object
```javascript
{
  english: string,      // English word
  phonetic: string,     // IPA phonetic notation
  turkish: string,      // Turkish translation
  memory: string,       // "learning" | "almost" | "completed" | "difficult"
  count: number,        // Study occurrence counter
  type: string,         // Word type (noun, verb, adjective, etc.)
  repetitions: number   // Repetition counter
}
```

### Memory Status Values
- `completed` - Word is fully learned
- `almost` - Almost memorized
- `learning` - Currently being learned
- `difficult` - Marked as difficult

## Key Functions Reference

### Authentication (`index.html:1043-1136`)
- `initSupabase()` - Initialize Supabase client
- `handleAuth()` - Login/signup handler
- `updateAuthUI()` - Update authentication UI state
- `logout()` - Sign out user

### Data Persistence (`index.html:1138-1217`)
- `loadWordsFromSupabase()` - Fetch vocabulary from cloud
- `saveWordsToSupabase()` - Persist vocabulary to cloud
- `saveToLocalStorage()` / `loadFromLocalStorage()` - Local backup

### Learning Mode (`index.html:1223-1330`)
- `showLearn()` - Display current word
- `speakWord(word, replay)` - TTS pronunciation (speeds: 0.7x, 0.9x)
- `updateLearnPool()` - Shuffle vocabulary for learning
- `updateLearnSessionInfo()` - Calculate progress statistics

### Speech Recognition (`index.html:1227-1300`)
- `initMicrophone()` - Request microphone permissions
- `startRecording()` / `stopRecording()` - Speech-to-text control

### Quiz Mode (`index.html:1331-1406`)
- `startQuiz()` - Initialize quiz with filtered word pool
- `nextQuiz()` - Display next question with 4 options
- `updateQuizStats()` - Track quiz performance

### Match Mode (`index.html:1408-1525`)
- `startMatch()` - Initialize matching game
- `loadMatchRound()` - Render word pairs
- `checkMatch()` - Validate matches

### Management (`index.html:1527-1592`)
- `showTable()` - Render vocabulary table
- `showStats()` - Display statistics

### Utilities
- `notif(msg, type)` - Toast notification system (`index.html:1031-1040`)
- `showMode(mode)` - Tab switching (`index.html:1594-1597`)

## Code Conventions

### Naming
- **Functions**: camelCase (e.g., `handleAuth`, `loadWordsFromSupabase`)
- **Variables**: camelCase (e.g., `currentUser`, `learnPool`)
- **CSS Classes**: kebab-case with semantic prefixes (e.g., `.btn-primary`, `.word-display`)
- **Constants**: Lowercase camelCase

### Style
- **Indentation**: 4 spaces
- **Semicolons**: Present (though inconsistent)
- **DOM Access**: Direct `getElementById`, `querySelector`
- **Async**: async/await pattern
- **Error Handling**: try-catch with toast notifications

### CSS Variables (Theme)
```css
--primary: #667eea      /* Purple-blue */
--primary-dark: #764ba2 /* Dark purple */
--success: #4facfe      /* Bright blue */
--warning: #ffa500      /* Orange */
--danger: #f5576c       /* Red-pink */
--secondary: #f093fb    /* Pink */
--dark: #1f2125         /* Near-black */
--light: #f5f5f5        /* Off-white */
```

## Important Global Variables

```javascript
supabase          // Supabase client instance
currentUser       // Authenticated user object
vocab[]           // Main vocabulary array (in-memory)
idx               // Current learn mode word index
qPool[]           // Quiz mode word selection pool
mPool[]           // Match mode word selection pool
learnPool[]       // Shuffled vocabulary for learning
selectedWords     // Set of selected words for bulk operations
recognition       // SpeechRecognition API instance
micReady          // Microphone availability status
isRecording       // Recording state flag
```

## Configuration

### Supabase Credentials (`index.html:999-1000`)
```javascript
const SUPABASE_URL = 'https://hcdzooubqhpxpewvbyxk.supabase.co';
const SUPABASE_KEY = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...';
```

**Note**: Credentials are currently hardcoded. For production, use environment variables.

## Development Guidelines

### When Making Changes

1. **Single File**: All changes go into `index.html`
2. **No Build Step**: Changes are immediately effective after page refresh
3. **Test Locally**: Use any static file server to test
4. **Browser DevTools**: Essential for debugging (no source maps)

### Adding New Words Programmatically
```javascript
vocab.push({
  english: 'example',
  phonetic: '/ɪɡˈzæmpəl/',
  turkish: 'ornek',
  memory: 'learning',
  count: 0,
  type: 'noun',
  repetitions: 0
});
saveWordsToSupabase(); // or saveToLocalStorage()
```

### Adding New UI Elements
1. Add HTML in the appropriate mode div (`#learn-mode`, `#quiz-mode`, etc.)
2. Add CSS in the `<style>` block
3. Add event listeners at the bottom of the script section

### Testing Checklist
- [ ] Authentication flow (signup/login/logout)
- [ ] Word display and pronunciation
- [ ] Quiz question generation
- [ ] Match pair validation
- [ ] Data persistence (cloud and local)
- [ ] CSV import/export
- [ ] Microphone permission handling
- [ ] Responsive design (test at 768px breakpoint)

## Known Issues

1. **Quiz Mode**: Hard-coded to 10 questions regardless of selection (line 1333)
2. **Memory Filters**: Filter buttons in quiz mode don't actually filter
3. **Match Mode Offset**: Calculation may be incomplete (line 1427)
4. **Security**: API keys are exposed in client-side code

## Browser Compatibility

| Browser | Support |
|---------|---------|
| Chrome/Edge 55+ | Full support |
| Firefox 53+ | Full (Web Speech may need polyfill) |
| Safari 11+ | Partial (Web Speech limited) |
| IE 11 | Not supported |

## Security Notes

**Current Issues (for improvement)**:
1. Supabase credentials are hardcoded in HTML (visible in source)
2. No input validation on CSV import
3. No CSP headers configured
4. Client-side only validation

**Recommendations**:
- Move credentials to environment variables with build-time injection
- Add input sanitization
- Implement Content Security Policy headers
- Consider server-side validation for critical operations

## Deployment

This is a static application that can be deployed to any static hosting:
- GitHub Pages
- Netlify
- Vercel
- Cloudflare Pages
- AWS S3 + CloudFront

Simply upload `index.html` and any referenced assets.

## Useful Commands

```bash
# Start local development server
python -m http.server 8000

# Or with live reload
npx live-server

# Check for syntax errors (if you have Node.js)
npx eslint index.html --ext .html
```

## Future Improvements

Potential areas for enhancement:
- Modular architecture (separate JS/CSS files)
- Build system integration (Vite recommended)
- TypeScript migration for type safety
- Automated testing framework
- Proper environment variable management
- Internationalization (i18n) support
- Spaced repetition algorithm implementation
