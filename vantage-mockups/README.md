# Vantage Configuration Mockups

Interactive UI prototypes for the Vantage condition monitoring redesign.

## About

These mockups demonstrate the proposed user experience for managing Vantage condition monitors in Seeq as part of **Epic CRAB-50387: Vantage: Improved flow for managing monitored conditions**.

Each mockup is a standalone HTML file that can be opened in any browser without requiring a build process or server.

## 📖 Getting Started

**New to creating mockups?** Read the comprehensive [GUIDE.md](./GUIDE.md) which covers:
- Setting up GitHub Pages
- Creating mockups from qomponents
- Mocking data flows using semantic graphs
- Using Seeq API patterns for realistic data
- Best practices and troubleshooting

## Available Mockups

### 1. Configuration Tab - Integrated V2
**File:** `vantage-configuration-tab-integrated-v2.html`
**CRAB:** CRAB-50387

A streamlined tabbed interface for configuring condition monitors, viewing discovered conditions, and monitoring execution history.

**Data Flow:**
```
User → Configuration Tab → Schedule Settings → Condition Sources → Save →
Monitor Execution → Conditions Tab → Results Display → History Tab
```

**Features:**
- 4 interactive scenarios (Admin Full Control, Admin Setup, View-Only Access, View-Only No Config)
- 3 tabs (Configuration, Conditions, History)
- Full edit/view mode switching
- Pixel-perfect Seeq design system implementation

**User Scenarios:**
- 🔐 **Admin - Full Control**: Administrator managing an existing condition monitor
- ⚙️ **Admin - Initial Setup**: Administrator setting up a new monitor
- 👁️ **View Only - Read Access**: Non-admin user viewing existing configuration
- 🚫 **View Only - No Config**: Non-admin user when no monitor is configured

### 2. History Tab with Archives
**File:** `history-tab-with-archives.html`
**CRAB:** CRAB-50387

Comprehensive audit trail showing run history, archived condition monitors, and archived conditions with restore capabilities.

**Data Flow:**
```
Monitor Execution → Run History Storage → Archive Action →
Archived Monitors/Conditions Table → Restore/View Actions
```

**Features:**
- 3 interactive table sections (Run History, Archived Monitors, Archived Conditions)
- Full audit trail of monitor executions with status indicators (Completed/Failed)
- Archive management with metadata (archived by, date, reason)
- Action buttons for viewing details, history, and restoring archived items
- Pixel-perfect Seeq design system implementation

**Sections:**
- 📊 **Run History**: Past executions showing timestamp, status, duration, and conditions found
- 📦 **Archived Monitors**: Previously configured monitors with archive metadata and restore capability
- 🗃️ **Archived Conditions**: Individual conditions no longer being monitored with trigger counts

## Viewing Mockups Locally

Simply open any HTML file in your browser:
```bash
open docs/vantage-mockups/index.html
```

Or navigate to the file in your browser.

## Viewing on GitHub Pages

Once GitHub Pages is enabled for this repository, the mockups will be available at:
```
https://seeq12.github.io/crab/vantage-mockups/
```

### Enabling GitHub Pages

1. Go to the repository Settings
2. Navigate to "Pages" in the left sidebar
3. Under "Source", select "Deploy from a branch"
4. Choose the `develop` (or `main`) branch and `/docs` folder
5. Click "Save"

The site will be available within a few minutes at the URL above.

## Technical Details

### Design System
All mockups use the exact Seeq design system:
- Seeq CSS variables from `client/packages/qomponents/src/styles.css`
- Component styles from the qomponents library
- Tailwind CSS with Seeq theme configuration

### Component Fidelity
Each mockup component (Button, TextField, Tabs, Icon) is implemented to match the exact specifications from:
- `client/packages/qomponents/src/Button/Button.tsx`
- `client/packages/qomponents/src/TextField/TextField.tsx`
- `client/packages/qomponents/src/Tabs/Tabs.tsx`
- `client/packages/qomponents/src/Icon/Icon.tsx`

### No Build Required
All dependencies are loaded from CDNs:
- React 18
- Tailwind CSS
- Font Awesome 6
- Babel Standalone (for JSX)

## Adding New Mockups

To add a new mockup:

1. Create a new HTML file in this directory
2. Use `vantage-configuration-tab-integrated-v2.html` as a template
3. Update the component implementation
4. Add a card to `index.html` linking to the new mockup with:
   - The mockup title and description
   - A **Data Flow** section showing the workflow/data flow through the component
   - A CRAB tag: `<span class="crab-tag">CRAB-#####</span>` with the relevant ticket number
   - Relevant metadata tags (scenarios, features, etc.)
5. Update this README with details about the new mockup including the CRAB number and data flow

## Sharing

Share the GitHub Pages URL internally:
```
https://seeq12.github.io/crab/vantage-mockups/
```

Or share direct links to specific mockups:
```
https://seeq12.github.io/crab/vantage-mockups/vantage-configuration-tab-integrated-v2.html
```

## Source Files

The original qomponent source files are located at:
```
client/packages/qomponents/src/VantageConfigurationTabIntegratedV2/
client/packages/qomponents/src/HistoryTabWithArchives/
```

These mockups are prototypes for CRAB-50387 (Vantage: Improved flow for managing monitored conditions).
