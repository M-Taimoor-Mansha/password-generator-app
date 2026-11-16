# 🔐 Password Generator App

A lightweight, browser-based password generator built with **HTML5, CSS3, Tailwind CSS, and Vanilla JavaScript**.

The app lets users choose a password length, select the character groups to include, generate a password, view a simple strength indicator, and copy the result to the clipboard. Its interface is designed to be straightforward, responsive, and easy to use without installing dependencies or running a build process.

> **Security note:** This project currently uses JavaScript's `Math.random()` for password generation. `Math.random()` is not a cryptographically secure random number generator, so passwords produced by this implementation should not be relied on for protecting high-value or sensitive accounts. See [Security Considerations](#security-considerations).

![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=flat&logo=html5&logoColor=white)
![CSS3](https://img.shields.io/badge/CSS3-1572B6?style=flat&logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=flat&logo=javascript&logoColor=black)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-CDN-38B2AC?style=flat&logo=tailwindcss&logoColor=white)
![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [User Interface](#user-interface)
- [How Password Generation Works](#how-password-generation-works)
- [Password Strength Indicator](#password-strength-indicator)
- [Copy to Clipboard](#copy-to-clipboard)
- [Technology Stack](#technology-stack)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [How to Use](#how-to-use)
- [Design and Styling](#design-and-styling)
- [Browser Compatibility](#browser-compatibility)
- [Security Considerations](#security-considerations)
- [Current Scope and Limitations](#current-scope-and-limitations)
- [Possible Improvements](#possible-improvements)
- [Troubleshooting](#troubleshooting)
- [License](#license)
- [Author](#author)

---

## Overview

Password Generator App is a small front-end utility for creating random passwords based on user-selected character options.

Instead of requiring users to manually think of a password, the application combines selected character groups and generates a password within a configurable length range. A strength indicator gives a quick visual estimate based on the selected character groups and password length.

The application runs entirely in the browser. It does not require an account, backend service, database, package installation, or build step.

## Features

- **Adjustable password length:** Choose a length from 4 to 32 characters using a range slider.
- **Uppercase characters:** Include letters from A to Z.
- **Lowercase characters:** Include letters from a to z.
- **Numbers:** Include digits from 0 to 9.
- **Symbols:** Include the supported special-character set.
- **Character-type inclusion:** The generator attempts to include at least one character from every selected character group.
- **Instant generation:** Generate a new password with a button.
- **Automatic initial password:** A password is generated when the page initializes, using the default settings.
- **Password strength indicator:** Displays TOO WEAK, WEAK, MEDIUM, or STRONG according to the app's simple scoring rules.
- **Copy feedback:** The copy control displays a temporary “Copied!” message after the copy operation succeeds.
- **Responsive layout:** The controls adapt from a single-column layout on smaller screens to a two-column layout on wider screens.
- **Lightweight implementation:** Uses native browser technologies without a framework or package manager.

## User Interface

The interface is organized into three main areas.

### 1. Password Display

The top panel displays the currently generated password in a monospaced font.

It also contains a copy button. After a copy attempt succeeds, the copy icon is temporarily replaced by a “Copied!” message.

### 2. Generator Controls

The controls panel contains:

| Control | Description | Current setting |
|---|---|---|
| Character Length | Range slider for password size | 12 characters by default |
| Minimum Length | Shortest supported password | 4 characters |
| Maximum Length | Longest supported password | 32 characters |
| Uppercase | Includes A-Z | Enabled by default |
| Lowercase | Includes a-z | Enabled by default |
| Numbers | Includes 0-9 | Enabled by default |
| Symbols | Includes supported special characters | Disabled by default |

The selected length is displayed next to the slider and updates as the slider moves.

### 3. Password Strength

The strength panel displays a text label and a four-segment visual indicator.

The current implementation evaluates the number of enabled character groups and adds points for longer password lengths. This is a simple interface heuristic, not a formal password-strength or entropy calculation.

---

## How Password Generation Works

The generation logic is implemented in `script.js`.

### Supported character groups

The application defines four character sets:

| Group | Characters |
|---|---|
| Uppercase | `ABCDEFGHIJKLMNOPQRSTUVWXYZ` |
| Lowercase | `abcdefghijklmnopqrstuvwxyz` |
| Numbers | `0123456789` |
| Symbols | `!@#$%^&*()_+-=[]{}|;:,.<>?` |

Users can enable or disable each group independently.

### Generation process

When the Generate Password button is clicked, the application:

1. Reads the selected password length.
2. Checks which character groups are enabled.
3. Combines the enabled groups into one character pool.
4. Checks whether at least one character group is selected.
5. Selects one character from each enabled group.
6. Fills the remaining positions using random selections from the combined pool.
7. Combines the guaranteed characters with the remaining characters.
8. Shuffles the resulting characters.
9. Displays the generated password.
10. Refreshes the strength indicator.

### No character group selected

If all four options are disabled, the application displays:

```text
Select options
```

It does not generate a password until at least one character group is selected.

### Default configuration

When the page first loads, the current interface uses:

- Length: 12 characters
- Uppercase: enabled
- Lowercase: enabled
- Numbers: enabled
- Symbols: disabled

The application generates an initial password automatically using these defaults.

---

## Password Strength Indicator

The strength indicator is updated when the user changes the password length, changes a character option, or generates a password.

The current scoring approach works as follows:

- Add one point for each enabled character group.
- Add one point when the selected length is at least 12 characters.
- Add another point when the selected length is at least 16 characters.

The score is mapped to a label:

| Score | Display |
|---|---|
| 0-1 | TOO WEAK |
| 2 | WEAK |
| 3-4 | MEDIUM |
| 5-6 | STRONG |

There is also an additional low-length rule: a password shorter than 8 characters with fewer than three points is displayed as TOO WEAK.

The strength bars use red for very weak results, yellow for medium results, and emerald green for strong results.

**Important:** This indicator is a basic visual heuristic. It does not analyze the generated password's actual entropy, randomness quality, known password patterns, dictionary words, or resistance to modern cracking techniques. The label should not be treated as a security guarantee.

---

## Copy to Clipboard

The copy button reads the current value from the password display and attempts to copy it.

The current implementation:

1. Checks that a password is displayed.
2. Ignores the placeholder state `Select options`.
3. Creates a temporary textarea element.
4. Places the displayed password in the textarea.
5. Selects the textarea content.
6. Calls the legacy `document.execCommand('copy')` method.
7. Displays the “Copied!” feedback.
8. Removes the temporary textarea.
9. Restores the copy icon after approximately two seconds.

The implementation uses `document.execCommand('copy')` as a compatibility fallback. This API is deprecated. A future improvement would be to use the modern Clipboard API (`navigator.clipboard.writeText()`) where available, with a fallback for supported environments.

---

## Technology Stack

| Technology | Purpose |
|---|---|
| HTML5 | Page structure, form controls, labels, and buttons |
| CSS3 | Custom range slider and checkbox styling |
| Tailwind CSS | Utility classes for layout, spacing, colors, typography, and responsive behavior |
| Vanilla JavaScript (ES6+) | Password generation, event handling, strength scoring, and copy interaction |
| Google Fonts | Inter and JetBrains Mono typography |
| Browser DOM APIs | Reading controls, updating the interface, and handling user interaction |

### No framework or build system

The project is a static front-end application. It does not currently use React, Vue, Angular, Node.js, npm, a bundler, or a backend.

Tailwind CSS is loaded through its CDN script in `index.html`:

```html
<script src="https://cdn.tailwindcss.com"></script>
```

The custom stylesheet is loaded separately through `styles.css`.

---

## Project Structure

```text
password-generator-app/
│
├── index.html       # Application markup and user interface
├── styles.css       # Custom range slider and checkbox styling
├── script.js        # Password generation and interactive behavior
├── LICENSE          # MIT License
└── README.md        # Project documentation
```

### File descriptions

| File | Responsibility |
|---|---|
| `index.html` | Defines the password display, length slider, character options, strength indicator, and buttons |
| `styles.css` | Defines the Inter font family and custom styling for the range slider and checkboxes |
| `script.js` | Handles password generation, strength updates, length changes, and copy interactions |
| `LICENSE` | Contains the MIT License |
| `README.md` | Explains the project, setup, behavior, and limitations |

---

## Getting Started

### Requirements

- A modern web browser such as Chrome, Edge, Firefox, or Safari.
- An internet connection to load Tailwind CSS from its CDN and Google Fonts.
- Git (optional).
- Visual Studio Code or another code editor (optional).

No additional dependencies or installation commands are required.

### 1. Clone the repository

Open a terminal and run:

```bash
git clone https://github.com/M-Taimoor-Mansha/password-generator-app.git
```

### 2. Navigate to the project directory

```bash
cd password-generator-app
```

### 3. Open the application

**Option A: Open directly**

Open `index.html` in a modern web browser.

**Option B: Visual Studio Code Live Server**

1. Open the project folder in Visual Studio Code.
2. Install the Live Server extension if it is not already installed.
3. Open `index.html`.
4. Right-click inside the editor.
5. Select **Open with Live Server**.

**Option C: Python local server**

If Python is installed, run:

```bash
python -m http.server 5500
```

Then open:

```text
http://localhost:5500
```

The Python command is only used to serve the static website locally. The application itself is not a Python project and does not contain a Python entry point.

---

## How to Use

1. Open the Password Generator App in your browser.
2. Adjust the Character Length slider to choose a length between 4 and 32.
3. Enable or disable Uppercase, Lowercase, Numbers, and Symbols.
4. Review the strength indicator as you change the settings.
5. Click **GENERATE PASSWORD**.
6. Review the generated password in the display panel.
7. Click the copy icon to copy the displayed password.
8. Wait for the temporary “Copied!” confirmation to disappear.
9. Change the options and generate another password whenever needed.

At least one character group must be enabled to generate a password.

---

## Design and Styling

The interface uses a dark slate background with emerald-green accents.

### Visual palette

| Color | Usage |
|---|---|
| Slate 900 (`#0f172a`) | Main page background and dark surfaces |
| Slate 800 (`#1e293b`) | Main cards and password display panel |
| Slate 700 (`#334155`) | Range slider track and inactive strength bars |
| Slate 300 (`#cbd5e1`) | Supporting text |
| Slate 100 (`#f1f5f9`) | Main headings and password text |
| Emerald 500 (`#10b981`) | Primary action and selected controls |
| Emerald 400 (`#34d399`) | Accent text and range slider thumb |
| Yellow 500 (`#eab308`) | Medium strength indicator |
| Red 500 (`#ef4444`) | Weak strength indicator |

### Typography

The project imports two Google Fonts:

- **Inter:** Used for general interface text.
- **JetBrains Mono:** Used for the generated password to improve character readability.

### Responsive layout

The main content is constrained to a maximum width of approximately 42rem. The generator controls use a single-column layout on smaller screens and a two-column grid at the medium breakpoint.

The password display allows long strings to wrap instead of overflowing the page.

---

## Browser Compatibility

The application uses standard HTML, CSS, and JavaScript features supported by modern browsers.

| Browser capability | Usage |
|---|---|
| HTML range input | Password length selection |
| HTML checkbox inputs | Character group selection |
| DOM event listeners | User interactions |
| CSS Grid and Flexbox | Responsive layout |
| CSS pseudo-elements | Custom range slider appearance |
| Clipboard fallback | Copy interaction using a temporary textarea and `execCommand` |

Tailwind CSS and Google Fonts are external resources in the current implementation. Their CDN availability affects the intended styling and typography.

---

## Security Considerations

Password generation is security-sensitive. It is important to understand the limitations of the current implementation.

### 1. Random number generation

The current generator uses JavaScript's `Math.random()` to select characters.

`Math.random()` is designed for general-purpose randomness and is not cryptographically secure. It should not be used as the randomness source for passwords intended to protect sensitive accounts.

For a security-focused implementation, use the browser's Web Crypto API, particularly `crypto.getRandomValues()`, with an unbiased selection method.

### 2. Character shuffling

The current implementation shuffles the password using:

```javascript
password.split('').sort(() => 0.5 - Math.random()).join('');
```

This is not a uniformly reliable shuffle algorithm. A future security improvement should use the Fisher-Yates shuffle with a cryptographically secure random source.

### 3. Strength estimation

The strength indicator is based on selected options and length thresholds. It is not a formal security assessment and does not measure the actual randomness of a password.

### 4. Local processing

The current application has no backend integration and does not intentionally send generated passwords to a server. Password generation and display occur in the browser.

However, users should still avoid generating or copying sensitive credentials on devices or browsers they do not trust.

### 5. Clipboard behavior

The copy feature currently uses the deprecated `document.execCommand('copy')` method. Clipboard behavior can vary by browser and execution context. A modern implementation should use the Clipboard API and handle permission or browser errors explicitly.

**Recommendation:** Until cryptographically secure generation and unbiased shuffling are implemented and reviewed, treat this project as an educational front-end demonstration rather than a production-grade password manager or security tool.

---

## Current Scope and Limitations

| Capability | Current status |
|---|---|
| Password generation | Implemented |
| Adjustable length | Implemented, 4-32 characters |
| Uppercase selection | Implemented |
| Lowercase selection | Implemented |
| Number selection | Implemented |
| Symbol selection | Implemented |
| At least one character from each selected group | Implemented |
| Automatic password on page load | Implemented |
| Strength indicator | Implemented, heuristic only |
| Copy interaction | Implemented using legacy copy API |
| Copy confirmation | Implemented |
| Responsive interface | Implemented |
| Cryptographically secure random generation | Not implemented |
| Unbiased cryptographic shuffle | Not implemented |
| Password history | Not implemented |
| Saved passwords | Not implemented |
| User accounts | Not implemented |
| Backend or database | Not included |
| Password synchronization | Not included |
| Password manager functionality | Not included |

---

## Possible Improvements

The following are potential future enhancements. They are not currently implemented.

### Security improvements

- Replace `Math.random()` with `crypto.getRandomValues()`.
- Implement unbiased cryptographic random index selection.
- Replace the random sort comparator with a Fisher-Yates shuffle.
- Add tests for character selection and generation edge cases.
- Improve the strength estimator and clearly explain its methodology.
- Use the modern Clipboard API with error handling.

### User experience

- Add an option to exclude ambiguous characters such as `O`, `0`, `I`, and `l`.
- Add a setting to exclude similar-looking symbols.
- Add a password visibility toggle.
- Add a regenerate shortcut.
- Improve accessible status announcements for generated and copied passwords.
- Add a clearer message when all character groups are disabled.
- Add a visual indication of the selected slider value.

### Additional functionality

- Add passphrase generation.
- Add configurable custom character sets.
- Add a copy failure message.
- Add an option to generate multiple passwords.
- Add a password entropy estimate with clearly documented assumptions.
- Add automated unit and browser tests.

---

## Troubleshooting

### The page opens without styling

Check that `styles.css` is in the project root and that `index.html` contains:

```html
<link rel="stylesheet" href="styles.css" />
```

Also check your internet connection because Tailwind CSS and Google Fonts are loaded externally.

### The Generate Password button does nothing

Make sure `script.js` exists in the project root and is included at the end of `index.html`:

```html
<script src="script.js"></script>
```

Open the browser developer console to check for JavaScript errors.

### The app displays “Select options”

All character groups are disabled. Enable at least one of Uppercase, Lowercase, Numbers, or Symbols, then generate the password again.

### The copy button does not copy the password

Clipboard access can depend on browser security restrictions and execution context. Try opening the project through localhost using Live Server or the Python local server. If the problem continues, check the browser console and clipboard permissions.

### The strength label does not change as expected

The strength indicator uses a simple score based on selected character groups and length thresholds. It does not inspect password entropy or compare the generated password against known weak-password patterns.

---

## License

This project is available under the **MIT License**.

Copyright (c) 2026 CodewithTaimoor.

You may use, copy, modify, merge, publish, distribute, sublicense, and sell copies of the software, subject to the conditions of the MIT License.

See the [LICENSE](LICENSE) file for the complete license text.

---

## Author

**Muhammad Taimoor Mansha**

- GitHub: [@M-Taimoor-Mansha](https://github.com/M-Taimoor-Mansha)
- LinkedIn: [Muhammad Taimoor Mansha](https://linkedin.com/in/muhammad-taimoor-mansha-a09455228)
- Portfolio: [Personal Portfolio](https://m-taimoor-mansha.github.io/Portfolio/)

---

<p align="center">
  Built with HTML5, CSS3, Tailwind CSS, and Vanilla JavaScript.
  <br><br>
  <strong>Password Generator App</strong>
  <br>
  Simple password generation, right in your browser.
</p>
