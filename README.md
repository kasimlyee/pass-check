# Pass Check

[![npm version](https://badge.fury.io/js/pass-check.svg)](https://badge.fury.io/js/pass-check)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![CI](https://github.com/kasimlyee/pass-check/actions/workflows/ci.yml/badge.svg)](https://github.com/kasimlyee/pass-check/actions/workflows/ci.yml)

A password strength evaluation library with real-time feedback, customizable rules, and UI components.

## Features

- Real-time password strength evaluation
- Cstomizable scoring system and rule weights
- Multi-level strength indicators (Very Weak to Excellent)
- Password entropy calculation
- Character variety checks (uppercase, lowercase, digits, symbols)
- Minimum and maximum length enforcement
- Detection of common passwords and dictionary words
- Pattern detection (repeats, sequences, keyboard patterns)
- Blacklist personal information
- Plugin system for custom rules
- Vanilla JS, React, and Vue support
- Customizable UI components
- Internationalization support

## Installation

```bash
npm install pass-check
# or
yarn add pass-check
# or
pnpm add pass-check
```

## Usage

### Core API

```typescript
import { evaluatePassword } from 'pass-check';

const result = evaluatePassword('your-password', {
  minLength: 8,
  maxLength: 64,
  userInputs: ['yourname', 'yourcompany'], // Blacklist these words
  ruleWeights: {
    length: 2, // Give more weight to length
    common: 3  // And even more to common password check
  }
});

console.log(result);
/*
{
  score: 78,
  strength: 'strong',
  percentage: 78,
  feedback: {
    suggestions: ['Add special characters'],
    warnings: []
  },
  details: [...]
}
*/
```

### React Component

```tsx
import { PassStrengthMeter, usePassStrength } from 'pass-check/react';

function PasswordForm() {
  const [password, setPassword] = useState('');
  const [email, setEmail] = useState('');
  
  // Or use the hook directly:
  const strength = usePassStrength(password, {
    userInputs: [email.split('@')[0]] // Blacklist username part of email
  });

  return (
    <div>
      <input
        type="password"
        value={password}
        onChange={(e) => setPassword(e.target.value)}
      />
      
      <PassStrengthMeter 
        password={password}
        options={{
          userInputs: [email.split('@')[0]]
        }}
        showFeedback
      />
    </div>
  );
}
```

### Vanilla JS

```html
<script src="https://unpkg.com/pass-check/dist/index.umd.js"></script>

<input type="password" id="password">
<div id="meter"></div>

<script>
  const { createPasswordMeter } = PasswordMeter;
  
  const meter = createPasswordMeter({
    container: '#meter',
    input: '#password',
    showFeedback: true,
    minLength: 10,
    blacklist: ['companyname']
  });
</script>
```

## API Reference

### Core Function

#### `evaluatePassword(password: string, options?: PasswordOptions): PasswordResult`

Evaluates password strength and returns detailed results.

**Options:**

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `minLength` | `number` | 8 | Minimum password length |
| `maxLength` | `number` | 64 | Maximum password length |
| `blacklist` | `string[]` | `[]` | Blacklisted words/phrases |
| `userInputs` | `string[]` | `[]` | User-specific info to blacklist |
| `ruleWeights` | `Record<string, number>` | `{}` | Custom weights for rules |
| `disabledRules` | `string[]` | `[]` | Rules to disable |
| `dictionary` | `Set<string>` | Common passwords | Custom dictionary |
| `i18n` | `Record<string, string>` | English messages | Custom translations |

**Result:**

```typescript
interface PasswordResult {
  score: number;               // Raw score
  strength: PasswordStrength;  // 'very-weak' | 'weak' | 'moderate' | 'strong' | 'excellent'
  percentage: number;          // Score as percentage (0-100)
  feedback: {
    suggestions: string[];     // How to improve
    warnings: string[];        // Critical issues
  };
  details: Array<{             // Detailed rule results
    ruleName: string;
    isValid: boolean;
    message?: string;
    score: number;
  }>;
}
```

### React API

#### `usePassStrength(password: string, options?: PasswordOptions): PasswordResult`

React hook that returns password strength results.

#### `<PassStrengthMeter password={string} options? showFeedback? className? barClassName? />`

React component that renders a visual strength meter.

### Vanilla JS API

#### `createPasswordMeter(options: MeterOptions): { update, destroy }`

Creates a password strength meter UI bound to an input field.

**Options:**

| Option | Type | Required | Description |
|--------|------|----------|-------------|
| `container` | `HTMLElement | string` | Yes | Where to render the meter |
| `input` | `HTMLInputElement | string` | Yes | Password input to monitor |
| `showFeedback` | `boolean` | No | Whether to show suggestions |
| `customCSS` | `boolean` | No | Disable default styles |
| All `PasswordOptions` | | No | Same as core options |

## Customization

### Custom Rules

```typescript
import { evaluatePassword, getDefaultRules } from 'pass-check';

const customRule = {
  name: 'no-repeated-chars',
  description: 'No more than 2 repeated characters',
  validate: (password) => {
    const hasRepeats = /(.)\1{2,}/.test(password);
    return {
      isValid: !hasRepeats,
      score: hasRepeats ? 0 : 10,
      message: 'Too many repeated characters'
    };
  },
  weight: 1.5
};

const rules = [...getDefaultRules(), customRule];
const result = evaluatePassword('password', {}, rules);
```

### Custom Translations

```typescript
evaluatePassword('password', {
  i18n: {
    'very-weak': 'Muy débil',
    'weak': 'Débil',
    'common': 'Contraseña común',
    // ... other translations
  }
});
```

### Custom UI Styles

```css
/* Override default styles */
.pass-meter-bar {
  height: 8px;
  border-radius: 4px;
}

.pass-meter-suggestions {
  color: #666;
  font-size: 0.9em;
}
```


## License

MIT © [Kasim Lyee --> Do what you want!!!]
