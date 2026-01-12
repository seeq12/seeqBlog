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

### 3. Condition Preview Modal
**File:** `condition-preview-modal.html`
**CRAB:** CRAB-50387

Modal dialog for previewing and selecting conditions from search results before creating a monitor, with filtering and bulk selection capabilities.

**Data Flow (Simple):**
```
Search Criteria → Execute Search → Item Finder API →
Condition Results Table → User Selection → Confirm & Create Monitor
```

**Semantic Dataflow (Detailed):**
```
┌────────────────────────┐
│ User Search Criteria   │ (SOURCE)
│ - Property Searches    │
│ - Search Types         │
│ - Predicates           │
└───────────┬────────────┘
            │ [HTTP POST /items/search, STATELESS]
            ↓
┌────────────────────────┐
│ Item Finder Search API │ (DERIVED)
│ - Parse Search Payload │
│ - Query Database       │
│ - Apply Predicates     │
│ - Filter by Type       │
│ - Apply Limit          │
└───────────┬────────────┘
            │ [transform operation, MONOTONIC]
            ↓
┌────────────────────────┐
│ Search Results Set     │ (DERIVED)
│ - Condition Items      │
│ - Metadata (path, etc) │
│ - Pagination Info      │
└───────────┬────────────┘
            │ [render operation]
            ↓
┌────────────────────────┐
│ Results Table Display  │ (SINK)
│ - Render Rows          │
│ - Selection State      │
│ - Filter Controls      │
└───────────┬────────────┘
            │ [user interaction, STATEFUL]
            ↓
┌────────────────────────┐
│ User Selection         │ (SOURCE)
│ - Selected IDs         │
│ - Validation Rules     │
└───────────┬────────────┘
            │ [HTTP POST /monitors, STATEFUL]
            ↓
┌────────────────────────┐
│ Create Monitor Request │ (SINK)
│ - Monitor Config       │
│ - Selected Conditions  │
└────────────────────────┘
```

**API Structure (from seek-sdk-notebook):**

Reference: [`02-item-finder-search.ipynb`](https://github.com/seeq12/seek-sdk-notebook/blob/master/notebooks/02-item-finder-search.ipynb)

```typescript
// Request Payload
interface ItemFinderSearchesInputV1 {
  propertySearches: PropertySearchV1[];
  fixedListSearches?: FixedListSearchV1[];
  limit: number;        // default: 40
  offset?: number;      // for pagination
}

interface PropertySearchV1 {
  searchType: 'PROPERTY';
  isInclude: boolean;   // true = AND/include, false = NOT/exclude
  predicates: PredicateV1[];
  types: string[];      // ['Condition', 'Signal', etc.]
}

interface PredicateV1 {
  propertyName: string;      // 'Name', 'Description', etc.
  operator: string;          // 'STRING_CONTAINS', 'EQUALS', etc.
  values: { value: string }[];
}

// Response
interface ItemFinderSearchesOutputV1 {
  items: ItemOutputV1[];
  hasMore: boolean;
  offset?: number;     // Next page offset
}
```

**Example Search Payload (from notebook):**
```typescript
// Search for conditions containing "Temperature" OR "Pressure" in name
{
  propertySearches: [
    {
      searchType: 'PROPERTY',
      isInclude: true,
      predicates: [{
        propertyName: 'Name',
        operator: 'STRING_CONTAINS',
        values: [
          { value: 'Temperature' },
          { value: 'Pressure' }
        ]
      }],
      types: ['Condition']
    }
  ],
  fixedListSearches: [],
  limit: 40
}
```

**Pagination Pattern (from notebook):**
```typescript
// Initial request
const page1 = await itemsApi.executeItemFinderSearches(searchPayload);
// Returns: { items: [...], hasMore: true, offset: 123 }

// Fetch next page
const page2Payload = { ...searchPayload, offset: page1.data.offset };
const page2 = await itemsApi.executeItemFinderSearches(page2Payload);
```

**Key API Patterns from Notebook:**
- Multiple predicates within one search = AND logic
- Multiple property searches = OR logic
- `isInclude: false` = NOT/exclude logic
- Supports multiple item types in single search
- Pagination via `limit`, `offset`, and `hasMore` flag

**Features:**
- Modal dialog with search criteria display
- Interactive search execution with loading state
- Results table with checkboxes for condition selection
- Real-time filtering by text and status (Active/Archived)
- Bulk selection controls (Select All / Deselect All)
- Selection count and validation
- Confirm button with disabled state when no conditions selected

**User Workflow:**
1. View search criteria that will be executed
2. Click "Execute Search" to fetch conditions from Item Finder API
3. Review returned conditions with metadata (workbench, formula, status)
4. Filter results using text search or Active/Archived toggles
5. Select/deselect individual conditions or use bulk controls
6. Confirm selection to create the monitor

### 4. Condition Preview Inline
**File:** `condition-preview-inline.html`
**CRAB:** CRAB-50387

Inline configuration view showing monitor settings, condition search criteria, and preview results all in one page layout with section-based organization.

**Data Flow:**
```
Configuration Settings → Condition Sources Input → Execute Search →
Item Finder API → Results Table → Create Monitor
```

**Features:**
- Three color-coded sections (Configuration, Condition Sources, Preview Results)
- Configuration display: Look Back/Look Ahead Duration, Cron Schedule
- Condition search criteria with visual highlighting
- Same interactive results table as modal version
- All features in one scrollable page view
- Section headers with distinct styling (blue for config, green for results)

**Sections:**
- ⚙️ **Configuration** (Blue): Monitor execution settings and schedules
- 🔍 **Condition Sources** (Blue): Search criteria for finding conditions
- ✅ **Preview Results** (Green): Interactive table with search execution and selection

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
client/packages/qomponents/src/ConditionPreviewModal/
client/packages/qomponents/src/ConditionPreviewInline/
```

These mockups are prototypes for CRAB-50387 (Vantage: Improved flow for managing monitored conditions).
