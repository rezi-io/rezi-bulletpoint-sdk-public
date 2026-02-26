<!-- @format -->

## Use CDN

After publishing to GitHub, you can use jsDelivr for faster, global delivery:

### Latest Version (Development)

```html
<script src="https://cdn.jsdelivr.net/gh/rezi-io/rezi-bulletpoint-sdk-public/rezi-bullet-sdk.min.js"></script>
```

### Specific Commit (Production)

```html
<script src="https://cdn.jsdelivr.net/gh/rezi-io/rezi-bulletpoint-sdk-public@COMMIT_HASH/rezi-bullet-sdk.min.js"></script>
```

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

		<script src="https://cdn.jsdelivr.net/gh/rezi-io/rezi-bulletpoint-sdk-public/rezi-bullet-sdk.min.js"></script>
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

| Attribute               | Description                                                | Required              |
| ----------------------- | ---------------------------------------------------------- | --------------------- |
| `data-bullet-generator` | Marks the element as the SDK container                     | ✅ Yes                |
| `data-recaptcha-key`    | reCAPTCHA Enterprise site key for API access               | ✅ Yes                |
| `data-cta-url`          | URL to redirect when CTA is clicked or rate limit reached  | ❌ No                 |
| `data-max-skills`       | Maximum number of skills a user can select                 | ❌ No (default: `5`)  |
| `data-dev`              | Enable development mode (console logs, dev API endpoints)  | ❌ No (default: off)  |

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
			localStorage.setItem('resume_prefill', JSON.stringify({ jobTitle, bullets }));
		},
	});
</script>
```

## Configuration Options

```typescript
interface BulletPointConfig {
	containerSelector: string;          // CSS selector for the widget container
	isDev?: boolean;                    // Use dev API endpoints and enable console logs
	ctaUrl?: string;                    // URL for the CTA button redirect
	maxSkillSelection?: number;         // Max selectable skills (default: 5)
	recaptchaSiteKey?: string;          // reCAPTCHA Enterprise site key
	onGenerate?: (bullets: string[]) => void;                  // Bullets generated
	onCopy?: (bullet: string) => void;                         // Bullet copied
	onCTAClick?: (bullets: string[], jobTitle: string) => void; // CTA clicked
	onError?: (error: Error) => void;                          // Error occurred
}
```

## Styling

The SDK bundles its own styles — no separate CSS file is required.

### CSS Variables

```css
:root {
	--primary: #4d70eb;             /* Primary brand color */
	--primary-light: #c6c8ff;       /* Light primary (selected chip bg) */
	--rezi-bule-500: #6b84fe;       /* Primary hover color */
	--success: #22c55e;             /* Success state color */
	--error: #ef4444;               /* Error state color */
	--border: #e5e7eb;              /* Border color */
	--rounded-lg: 8px;              /* Widget border radius */
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

## Error Handling

The SDK fails silently and uses fallback content to ensure users always see bullet points:

```javascript
const generator = new ReziBulletSDK.BulletPointGenerator({
	containerSelector: '#container',
	ctaUrl: 'https://app.rezi.ai/signup',

	onError: (error) => {
		console.error('SDK error:', error);

		// Common scenarios (all handled automatically):
		// - Rate limit exceeded → user redirected to ctaUrl
		// - API unavailable → generic fallback bullets shown
		// - reCAPTCHA failure → API called without token (graceful)
	},
});
```
