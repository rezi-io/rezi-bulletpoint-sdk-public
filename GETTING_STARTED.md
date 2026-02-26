# Rezi Bullet Point SDK - Getting Started

### Simple Auto-initialization

The easiest way to add the bullet point generator to your website:

```html
<!doctype html>
<html>
  <head>
    <!-- No separate CSS needed — styles are bundled in the script -->
  </head>
  <body>
    <!-- Bullet Point Generator Container -->
    <div
      data-bullet-generator
      data-recaptcha-key="YOUR_RECAPTCHA_SITE_KEY"
      data-cta-url="https://app.rezi.ai/signup"
      data-max-skills="5"
    ></div>

    <script src="https://cdn.jsdelivr.net/npm/@rezi/bulletpoint-sdk/rezi-bullet-sdk.min.js"></script>
  </body>
</html>
```

The SDK will automatically:

1. Detect the `data-bullet-generator` element
2. Render the 2-column widget (job details + bullet results)
3. Load skill suggestions when a job title is entered
4. Generate AI bullet points on demand
5. Allow copying individual bullets to clipboard

### HTML Attributes

| Attribute               | Description                                               | Required             |
| ----------------------- | --------------------------------------------------------- | -------------------- |
| `data-bullet-generator` | Marks the element as the SDK container                    | ✅ Yes               |
| `data-recaptcha-key`    | reCAPTCHA Enterprise site key for API access              | ✅ Yes               |
| `data-cta-url`          | URL to redirect when CTA is clicked or rate limit reached | ✅ Yes               |
| `data-max-skills`       | Maximum number of skills a user can select                | ❌ No (default: `5`) |
| `data-dev`              | Enable development mode (console logs, dev API endpoints) | ❌ No (default: off) |

## Manual Initialization

For more control over the widget:

```html
<div id="my-container"></div>

<script>
  const generator = new ReziBulletSDK.BulletPointGenerator({
    containerSelector: '#my-container',
    ctaUrl: 'https://app.rezi.ai/signup',
    maxSkillSelection: 5,
    isDev: false,

    // Called when bullet points are generated
    onGenerate: (bullets) => {
      console.log('Generated bullets:', bullets);
    },

    // Called when a bullet is copied to clipboard
    onCopy: (bullet) => {
      console.log('Copied:', bullet);
    },

    // Called when CTA button is clicked (before redirect)
    onCTAClick: (bullets, jobTitle) => {
      localStorage.setItem(
        'resume_prefill',
        JSON.stringify({ jobTitle, bullets }),
      );
    },

    // Called when an error occurs
    onError: (error) => {
      console.error('SDK error:', error.message);
    },
  });
</script>
```

## Configuration Options

```typescript
interface BulletPointConfig {
  containerSelector: string; // CSS selector for the widget container
  isDev?: boolean; // Use dev API endpoints and enable console logs
  ctaUrl?: string; // URL for the CTA button redirect
  maxSkillSelection?: number; // Max selectable skills (default: 5)
  recaptchaSiteKey?: string; // reCAPTCHA Enterprise site key
  onJobTitleChange?: (title: string) => void; // Job title changed
  onSkillsLoaded?: (skills: string[]) => void; // Skills loaded from API
  onGenerate?: (bullets: string[]) => void; // Bullets generated
  onCopy?: (bullet: string) => void; // Bullet copied
  onCTAClick?: (bullets: string[], jobTitle: string) => void; // CTA clicked
  onError?: (error: Error) => void; // Error occurred
}
```

## Experience Levels

The widget includes an experience level selector with the following options:

| Value              | Label            |
| ------------------ | ---------------- |
| `internship`       | Internship       |
| `entry-level`      | Entry Level      |
| `associate`        | Associate        |
| `junior-level`     | Junior Level     |
| `mid-senior-level` | Mid-Senior Level |
| `director`         | Director         |
| `executive`        | Executive        |

## Styling

The SDK bundles its own styles — no separate CSS file is required.

### CSS Variables

You can override the SDK's appearance using CSS variables on a parent element or `:root`:

```css
:root {
  --primary: #4d70eb; /* Primary brand color */
  --primary-light: #c6c8ff; /* Light primary (selected chip bg) */
  --rezi-bule-500: #6b84fe; /* Primary hover color */
  --success: #22c55e; /* Success state color */
  --error: #ef4444; /* Error state color */
  --border: #e5e7eb; /* Border color */
  --rounded-sm: 4px; /* Small border radius */
  --rounded-md: 6px; /* Medium border radius */
  --rounded-lg: 8px; /* Large border radius */
  --font-family: 'Source Sans Pro', sans-serif; /* Widget font */
}
```

### Custom Styling

```css
/* Override the widget container */
[data-bullet-generator] .wrapper {
  border-radius: 12px;
  box-shadow: 0 4px 24px rgba(0, 0, 0, 0.08);
}

/* Override skill chip selected state */
[data-bullet-generator] .skill-chip.selected {
  background: #your-brand-color;
  border-color: #your-brand-color;
}

/* Override the generate button */
[data-bullet-generator] .button:not(:disabled) {
  background: #your-brand-color;
}
```

## Advanced Usage

### Pre-filling Job Title

```javascript
const generator = new ReziBulletSDK.BulletPointGenerator({
  containerSelector: '#container',
  ctaUrl: 'https://app.rezi.ai/signup',
});

// Access widget state programmatically
const state = generator.getState();
console.log('Current job title:', state.jobTitle);
console.log('Selected skills:', state.selectedSkills);
console.log('Generated bullets:', state.bullets);
```

### Persisting Bullets for Sign-up Flow

```javascript
const generator = new ReziBulletSDK.BulletPointGenerator({
  containerSelector: '#container',
  ctaUrl: 'https://app.rezi.ai/signup',

  onCTAClick: (bullets, jobTitle) => {
    // Save generated data before redirect
    sessionStorage.setItem(
      'prefill_data',
      JSON.stringify({
        jobTitle,
        bullets,
      }),
    );
    // SDK will redirect to ctaUrl after this callback
  },
});
```

### Development Mode

For testing with dev API endpoints and verbose logging:

```html
<div
  data-bullet-generator
  data-dev
  data-recaptcha-key="YOUR_DEV_RECAPTCHA_KEY"
  data-cta-url="http://localhost:3000/signup"
></div>
```

Or programmatically:

```javascript
const generator = new ReziBulletSDK.BulletPointGenerator({
  containerSelector: '#container',
  isDev: true,
  ctaUrl: 'http://localhost:3000/signup',
});
```

## API Reference

### BulletPointGenerator Class

#### Constructor

```typescript
new BulletPointGenerator(config: BulletPointConfig)
```

#### Methods

```typescript
// Get the current widget state
getState(): WidgetState

// Destroy the widget and clean up event listeners
destroy(): void
```

#### WidgetState

```typescript
interface WidgetState {
  phase: 'idle' | 'loading-skills' | 'ready' | 'generating' | 'done';
  jobTitle: string;
  experienceLevel: string;
  selectedSkills: string[];
  bullets: string[];
}
```

### Utility Functions

```typescript
// Copy text to clipboard
ReziBulletSDK.copyToClipboard(text: string): Promise<boolean>

// Highlight metric placeholders in bullet text
ReziBulletSDK.highlightMetrics(text: string): string

// Escape HTML to prevent XSS
ReziBulletSDK.escapeHtml(text: string): string

// Get/set session storage
ReziBulletSDK.setSessionItem(key: string, value: string): void
ReziBulletSDK.getSessionItem(key: string): string | null

// Get/set local storage
ReziBulletSDK.setLocalItem(key: string, value: string): void
ReziBulletSDK.getLocalItem(key: string): string | null
```

## Rate Limiting

The SDK enforces client-side rate limits using `localStorage`:

- **Skill suggestions**: Resets daily
- **Bullet generation**: 3 requests per day per browser

When the bullet generation rate limit is reached, users are automatically prompted to sign up via the `ctaUrl`.

## Error Handling

```javascript
const generator = new ReziBulletSDK.BulletPointGenerator({
  containerSelector: '#container',

  onError: (error) => {
    console.error('SDK error:', error);

    // Common error scenarios:
    // - Rate limit exceeded → user is redirected to ctaUrl automatically
    // - API unavailable → fallback bullet templates are used
    // - reCAPTCHA failure → API call proceeds without token (graceful degradation)
  },
});
```

## Browser Support

- Chrome 70+
- Firefox 65+
- Safari 12+
- Edge 79+

## Examples

Check out the `example.html` file for a complete working example with all configuration options and manual initialization patterns.

## Support

For issues and questions:

- 📧 Email: support@rezi.ai
- 📚 Documentation: [docs.rezi.ai](https://docs.rezi.ai)
- 🐛 Bug Reports: [GitHub Issues](https://github.com/rezi-ai/sdk/issues)

## License

MIT License - see LICENSE file for details.
