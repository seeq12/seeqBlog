# Guide: Creating and Sharing Qomponent Mockups with GitHub Pages

A comprehensive guide for creating interactive qomponent mockups and sharing them via GitHub Pages, including how to mock realistic data flows.

## Table of Contents

1. [Setting Up GitHub Pages](#setting-up-github-pages)
2. [Creating a Mockup from Qomponents](#creating-a-mockup-from-qomponents)
3. [Mocking Data Flows](#mocking-data-flows)
4. [Using Seeq API Patterns for Realistic Data](#using-seeq-api-patterns-for-realistic-data)
5. [Adding Your Mockup to the Site](#adding-your-mockup-to-the-site)
6. [Best Practices](#best-practices)

---

## Setting Up GitHub Pages

### Initial Setup (One-Time)

1. **Enable GitHub Pages** for your repository:
   ```bash
   # Go to: https://github.com/seeq12/crab/settings/pages
   # Source: Deploy from a branch
   # Branch: develop
   # Folder: /docs
   # Click Save
   ```

2. **Create the `.nojekyll` file** (prevents Jekyll processing):
   ```bash
   touch docs/.nojekyll
   git add docs/.nojekyll
   git commit -m "NO-CRAB: Add .nojekyll for GitHub Pages"
   ```

3. **Your site will be available at**:
   ```
   https://seeq12.github.io/crab/vantage-mockups/
   ```

### Directory Structure

```
docs/
├── .nojekyll                    # Disables Jekyll processing
└── vantage-mockups/             # Your mockups folder
    ├── index.html               # Landing page
    ├── README.md                # Documentation
    ├── GUIDE.md                 # This guide
    └── your-mockup.html         # Individual mockups
```

---

## Creating a Mockup from Qomponents

### Step 1: Read the Qomponent Source

First, understand the component you want to mock:

```bash
# Example: Reading VantageConfigurationTabIntegratedV2
cat client/packages/qomponents/src/VantageConfigurationTabIntegratedV2/VantageConfigurationTabIntegratedV2.tsx
```

Key things to identify:
- **Props interface**: What configuration options does the component accept?
- **State variables**: What internal state does it manage?
- **Dependencies**: What other qomponents does it use (Button, Icon, Tabs, TextField)?
- **Styling**: What Tailwind classes and Seeq CSS variables are used?

### Step 2: Extract Seeq Design System Tokens

The qomponents library uses a custom Tailwind configuration with Seeq's design tokens:

```typescript
// From client/packages/qomponents/tailwind.config.cjs
colors: {
  'sq-color-dark': 'rgb(42, 92, 132)',        // Seeq brand blue
  'sq-text-color': 'rgb(58, 58, 58)',         // Primary text
  'sq-disabled-gray': 'rgb(221, 225, 227)',   // Borders/disabled
  'sq-light-gray': 'rgb(241, 245, 247)',      // Hover states
  'sq-danger-color': 'rgb(217, 83, 79)',      // Error/danger
  // ... and many more
}
```

Copy the exact CSS variables from `client/packages/qomponents/src/styles.css`:

```css
:root {
    --sq-color-dark: 42, 92, 132;
    --sq-text-color: 58, 58, 58;
    /* ... etc */
}
```

### Step 3: Create Mock Components

Create lightweight mock versions of the qomponent dependencies that match their APIs:

```javascript
// Mock Button Component (matching Button.tsx)
const Button = ({ variant = 'outline', label, icon, onClick, size = 'sm', disabled = false }) => {
  const baseClasses = 'tw-px-2.5 tw-rounded-md focus:tw-ring-0';
  const variantClasses = {
    'outline': 'tw-bg-white tw-border-sq-disabled-gray tw-text-sq-text-color',
    'theme': 'tw-bg-sq-color-dark tw-border-sq-color-dark tw-text-white',
    'danger': 'tw-bg-sq-danger-color tw-text-white',
  };
  // ... implementation
};

// Mock TextField Component (matching TextField.tsx)
const TextField = ({ label, value, disabled = false, onChange }) => {
  const appliedClasses = `tw-h-inputs tw-border-sq-disabled-gray tw-text-sm ${disabled ? 'tw-bg-sq-light-gray' : ''}`;
  // ... implementation
};

// Mock Icon Component (matching Icon.tsx)
const Icon = ({ icon, size = '1x', extraClassNames = '' }) => {
  const iconClass = icon.startsWith('fc-') ? `seeq-icon ${icon}` : `fa ${icon}`;
  // ... implementation
};
```

### Step 4: Build the Standalone HTML File

Create a complete HTML file with:

1. **CSS Variables** (Seeq design tokens)
2. **Tailwind Configuration** (matching qomponents config)
3. **React/Babel** from CDN
4. **Mock Components** (matching qomponent APIs)
5. **Your Component** (copied from source with minimal changes)
6. **Scenario Switcher** (optional, for demonstrating different states)

**Template Structure:**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Your Mockup Title</title>

  <!-- Seeq CSS Variables -->
  <style>
    :root {
      --sq-color-dark: 42, 92, 132;
      /* ... all Seeq variables */
    }
  </style>

  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  <script>
    tailwind.config = {
      prefix: 'tw-',
      theme: {
        extend: {
          colors: {
            'sq-color-dark': 'rgb(var(--sq-color-dark))',
            /* ... */
          }
        }
      }
    }
  </script>

  <!-- React -->
  <script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
  <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
</head>
<body>
  <div id="root"></div>

  <script type="text/babel">
    // Mock components here
    const Button = ({ ... }) => { ... };
    const TextField = ({ ... }) => { ... };

    // Your component here
    const YourComponent = ({ ... }) => { ... };

    // Render
    ReactDOM.createRoot(document.getElementById('root')).render(<YourComponent />);
  </script>
</body>
</html>
```

---

## Mocking Data Flows

### Understanding Semantic Dataflow

Seeq uses a **semantic graph approach** to represent data transformations. This approach separates:
- **WHAT** to compute (the logic/semantics)
- **HOW** to compute it (the execution strategy)

Based on the [semantic-dataflow notebooks](https://github.com/seeq12/seek-sdk-notebook/tree/master/notebooks/semantic-dataflow), here's how to apply this to mockups:

### Semantic Graph Structure

```typescript
// Nodes represent data or computation results
interface SemanticNode {
  id: string;
  type: 'SOURCE' | 'DERIVED' | 'SINK';
  label: string;
}

// Edges represent dependencies and operations
interface SemanticEdge {
  from: string;  // Source node
  to: string;    // Target node
  operation: {
    name: string;           // e.g., "filter", "aggregate", "transform"
    parameters: object;     // Operation-specific params
  };
  properties: {
    monotonicity: 'MONOTONIC' | 'NON_MONOTONIC';
    stateRequirement: 'STATELESS' | 'STATEFUL';
  };
}

// Complete program
interface SemanticProgram {
  nodes: SemanticNode[];
  edges: SemanticEdge[];
}
```

### Example: Condition Monitor Data Flow

For a Vantage condition monitor mockup, the semantic graph would be:

```javascript
const conditionMonitorDataFlow = {
  nodes: [
    { id: 'user-config', type: 'SOURCE', label: 'User Configuration' },
    { id: 'item-finder', type: 'DERIVED', label: 'Item Finder Search' },
    { id: 'condition-eval', type: 'DERIVED', label: 'Condition Evaluation' },
    { id: 'notification', type: 'SINK', label: 'Notifications' },
    { id: 'history', type: 'SINK', label: 'Run History' },
  ],
  edges: [
    {
      from: 'user-config',
      to: 'item-finder',
      operation: { name: 'search', parameters: { searchType: 'PROPERTY' } },
      properties: { monotonicity: 'MONOTONIC', stateRequirement: 'STATELESS' }
    },
    {
      from: 'item-finder',
      to: 'condition-eval',
      operation: { name: 'evaluate', parameters: { schedule: 'cron' } },
      properties: { monotonicity: 'NON_MONOTONIC', stateRequirement: 'STATEFUL' }
    },
    {
      from: 'condition-eval',
      to: 'notification',
      operation: { name: 'notify', parameters: { channels: ['email', 'slack'] } },
      properties: { monotonicity: 'MONOTONIC', stateRequirement: 'STATELESS' }
    },
    {
      from: 'condition-eval',
      to: 'history',
      operation: { name: 'store', parameters: { retention: '90d' } },
      properties: { monotonicity: 'MONOTONIC', stateRequirement: 'STATEFUL' }
    },
  ]
};
```

### Visualizing Data Flow in Mockups

#### Simple Arrow Notation (for cards)

```
User → Configuration Tab → Schedule Settings → Condition Sources →
Save → Monitor Execution → Conditions Tab → Results Display → History Tab
```

#### Detailed Semantic Graph (for documentation)

```
┌─────────────────┐
│ User Input      │ (SOURCE)
│ - Schedule      │
│ - Search Query  │
└────────┬────────┘
         │ [search operation]
         ↓
┌─────────────────┐
│ Item Finder API │ (DERIVED)
│ - Execute Query │
│ - Filter Results│
└────────┬────────┘
         │ [evaluate operation, STATEFUL]
         ↓
┌─────────────────┐
│ Condition Check │ (DERIVED)
│ - Run Monitor   │
│ - Detect Events │
└────────┬────────┘
         │ [branch]
         ├──────────────┐
         ↓              ↓
┌────────────┐  ┌──────────────┐
│ Notify     │  │ Store History│
│ (SINK)     │  │ (SINK)       │
└────────────┘  └──────────────┘
```

### Mocking State and Transitions

Use the semantic graph to mock realistic state transitions:

```javascript
const MockDataFlowSimulator = () => {
  const [currentNode, setCurrentNode] = useState('user-config');
  const [executionPath, setExecutionPath] = useState([]);

  const simulateFlow = () => {
    // Follow edges in the semantic graph
    const path = ['user-config', 'item-finder', 'condition-eval', 'notification'];
    setExecutionPath(path);

    // Simulate delays based on operation properties
    path.forEach((node, index) => {
      setTimeout(() => {
        setCurrentNode(node);
      }, index * 1000);
    });
  };

  return (
    <div>
      <h3>Data Flow Simulation</h3>
      <button onClick={simulateFlow}>Run Simulation</button>
      <div className="flow-visualization">
        {conditionMonitorDataFlow.nodes.map(node => (
          <div
            key={node.id}
            className={node.id === currentNode ? 'active' : ''}
          >
            {node.label}
          </div>
        ))}
      </div>
    </div>
  );
};
```

---

## Using Seeq API Patterns for Realistic Data

### Item Finder Search API

Reference: `notebooks/02-item-finder-search.ipynb`

The Item Finder API is commonly used in Vantage mockups for searching conditions and signals.

#### Basic Search Structure

```typescript
interface ItemFinderSearchesInputV1 {
  propertySearches: PropertySearch[];
  fixedListSearches: FixedListSearch[];
  limit: number;
  offset?: string;
}

interface PropertySearch {
  searchType: 'PROPERTY';
  isInclude: boolean;  // true = AND, false = NOT
  predicates: Predicate[];
  types: string[];     // ['Signal', 'Condition', etc.]
}

interface Predicate {
  propertyName: string;      // 'Name', 'Description', etc.
  operator: string;          // 'STRING_CONTAINS', 'EQUALS', etc.
  values: { value: string }[];
}
```

#### Mocking Search Results

```javascript
// Mock function matching Item Finder API
const mockItemFinderSearch = (searchPayload) => {
  // Simulate search based on predicates
  const { propertySearches, limit } = searchPayload;
  const search = propertySearches[0];
  const namePredicate = search.predicates.find(p => p.propertyName === 'Name');
  const searchTerm = namePredicate?.values[0]?.value || '';

  // Mock data that matches search patterns
  const mockItems = [
    {
      id: '0F0B407C-1E5F-77D0-A8DA-9B8AE03709E9',
      name: `Temperature_${searchTerm}_Sensor`,
      type: 'StoredSignal',
      description: 'Monitored signal from Vantage'
    },
    {
      id: '0F0B407C-1E6B-FB20-851A-1AE3E73575E4',
      name: `Pressure_${searchTerm}_Sensor`,
      type: 'StoredSignal',
      description: 'High priority condition'
    },
    // ... more mock items
  ];

  return {
    items: mockItems.slice(0, limit),
    hasMore: mockItems.length > limit,
    offset: mockItems.length > limit ? 'next-page-token' : undefined
  };
};
```

#### Using in Your Mockup

```javascript
const ConditionMonitorConfig = () => {
  const [searchResults, setSearchResults] = useState([]);

  const executeSearch = async () => {
    // In a real app, this would call the API
    // In the mockup, we simulate it
    const payload = {
      propertySearches: [{
        searchType: 'PROPERTY',
        isInclude: true,
        predicates: [{
          propertyName: 'Name',
          operator: 'STRING_CONTAINS',
          values: [{ value: 'Temperature' }]
        }],
        types: ['Signal']
      }],
      limit: 20
    };

    const results = mockItemFinderSearch(payload);
    setSearchResults(results.items);
  };

  return (
    <div>
      <button onClick={executeSearch}>
        Search for Temperature Signals
      </button>
      <div className="results">
        {searchResults.map(item => (
          <div key={item.id}>
            {item.name} ({item.type})
          </div>
        ))}
      </div>
    </div>
  );
};
```

### Mock Data Patterns

#### 1. Signals and Conditions

```javascript
const mockSignals = [
  { id: 'sig-001', name: 'Temperature Sensor A', type: 'Signal', unit: '°F' },
  { id: 'sig-002', name: 'Pressure Sensor B', type: 'Signal', unit: 'psi' },
  { id: 'cond-001', name: 'High Temperature Alert', type: 'Condition', capsuleCount: 12 },
];
```

#### 2. Time Series Data

```javascript
const mockTimeSeriesData = {
  samples: [
    { key: 1640000000000, value: 72.5 },
    { key: 1640003600000, value: 73.2 },
    { key: 1640007200000, value: 74.1 },
  ],
  units: '°F'
};
```

#### 3. Run History

```javascript
const mockRunHistory = [
  {
    id: 'run-001',
    timestamp: '2025-01-12T10:00:00Z',
    status: 'SUCCESS',
    itemsFound: 150,
    capsuleCount: 12,
    duration: '2.3s'
  },
  {
    id: 'run-002',
    timestamp: '2025-01-12T10:15:00Z',
    status: 'SUCCESS',
    itemsFound: 148,
    capsuleCount: 10,
    duration: '2.1s'
  },
];
```

---

## Adding Your Mockup to the Site

### Step 1: Create the HTML File

Save your mockup as `your-mockup-name.html` in `docs/vantage-mockups/`.

### Step 2: Update the Landing Page

Edit `docs/vantage-mockups/index.html` to add a card for your mockup:

```html
<!-- Your New Mockup Card -->
<a href="./your-mockup-name.html" class="mockup-card">
  <div class="mockup-content">
    <h3 class="mockup-title">Your Mockup Title</h3>
    <p class="mockup-description">
      A brief description of what this mockup demonstrates.
    </p>

    <div class="data-flow">
      <div class="data-flow-title">Data Flow</div>
      <div class="data-flow-content">
        User → Component A → Component B → Result
      </div>
    </div>

    <div class="mockup-meta">
      <span class="crab-tag">CRAB-#####</span>
      <span class="meta-tag">X Scenarios</span>
      <span class="meta-tag">Feature Tags</span>
    </div>
    <span class="btn-view">View Mockup →</span>
  </div>
</a>
```

### Step 3: Update the README

Add documentation for your mockup in `docs/vantage-mockups/README.md`:

```markdown
### 2. Your Mockup Title
**File:** `your-mockup-name.html`
**CRAB:** CRAB-#####

Brief description of the mockup.

**Data Flow:**
\`\`\`
User → Component A → Component B → Result
\`\`\`

**Features:**
- Feature 1
- Feature 2
- Feature 3

**User Scenarios:**
- Scenario 1 description
- Scenario 2 description
```

### Step 4: Commit and Push

```bash
git add docs/vantage-mockups/
git commit -m "NO-CRAB: Add [Your Mockup Name] mockup"
git push origin your-branch-name
```

After merging to `develop`, your mockup will be live at:
```
https://seeq12.github.io/crab/vantage-mockups/your-mockup-name.html
```

---

## Best Practices

### 1. Pixel-Perfect Accuracy

✅ **DO:**
- Copy exact class names from qomponent source
- Use exact Seeq CSS variable values
- Match button sizes, padding, and borders exactly
- Test in both light and dark themes (if applicable)

❌ **DON'T:**
- Approximate colors or spacing
- Use generic Tailwind classes without Seeq tokens
- Skip reading the actual qomponent implementation

### 2. Realistic Interactions

✅ **DO:**
- Implement state changes (edit mode, tab switching, etc.)
- Show loading states and transitions
- Include validation and error messages
- Simulate API responses with realistic timing

❌ **DON'T:**
- Create static, non-interactive mockups
- Use placeholder text like "Lorem ipsum"
- Skip error states or edge cases

### 3. Data Flow Documentation

✅ **DO:**
- Use semantic graph concepts (nodes, edges, operations)
- Include data flow diagrams in the landing page card
- Document the data flow in README
- Show state transitions visually in the mockup

❌ **DON'T:**
- Leave data flows implicit or undocumented
- Use vague descriptions like "data flows through system"
- Skip the semantic graph analysis

### 4. Scenario Coverage

✅ **DO:**
- Cover different user roles (admin, view-only, etc.)
- Show empty states and fully populated states
- Demonstrate error handling
- Include edge cases

❌ **DON'T:**
- Only show the "happy path"
- Forget about permissions and access control
- Skip initial setup vs. existing data scenarios

### 5. Performance

✅ **DO:**
- Load all dependencies from CDN (React, Tailwind, Font Awesome)
- Keep mockups under 500KB when possible
- Use browser caching for repeated loads
- Test on mobile/tablet viewports

❌ **DON'T:**
- Include large image files or videos
- Bundle unnecessary libraries
- Ignore mobile responsiveness

### 6. Maintenance

✅ **DO:**
- Update mockups when qomponent designs change
- Keep CRAB ticket references up-to-date
- Archive outdated mockups with clear notes
- Document breaking changes in README

❌ **DON'T:**
- Let mockups become stale without updates
- Remove mockups without explaining why
- Break existing URLs (use redirects if needed)

---

## Troubleshooting

### Issue: Colors Don't Match Seeq

**Solution:** Verify you're using RGB triplets correctly:
```css
/* Correct */
--sq-color-dark: 42, 92, 132;
.button { background: rgb(var(--sq-color-dark)); }

/* Incorrect */
--sq-color-dark: rgb(42, 92, 132);
.button { background: var(--sq-color-dark); }
```

### Issue: Tabs Don't Look Right

**Solution:** Check for the exact border and spacing from Tabs.tsx:
```css
tw-h-[25px]                    /* Exact height */
tw-border-b-[3px]              /* Active tab border */
tw-border-b-sq-color-dark      /* Active color */
tw-text-[0.875rem]             /* Font size */
```

### Issue: GitHub Pages Not Updating

**Solution:**
1. Verify you pushed to `develop` branch
2. Check that `.nojekyll` exists in `docs/`
3. Clear browser cache
4. Wait 2-3 minutes for GitHub Pages to rebuild

### Issue: Mock Data Feels Unrealistic

**Solution:**
1. Review actual API responses in notebooks/02-item-finder-search.ipynb
2. Use semantic graph patterns for state transitions
3. Add realistic delays: `setTimeout(() => setState(newState), 500)`
4. Include pagination, errors, and edge cases

---

## Resources

### Internal Documentation
- [Qomponents Source](../../client/packages/qomponents/src/)
- [Semantic Dataflow Tutorial](https://github.com/seeq12/seek-sdk-notebook/tree/master/notebooks/semantic-dataflow)
- [Item Finder Search Examples](https://github.com/seeq12/seek-sdk-notebook/blob/master/notebooks/02-item-finder-search.ipynb)

### External Resources
- [React 18 Documentation](https://react.dev/)
- [Tailwind CSS](https://tailwindcss.com/)
- [Font Awesome Icons](https://fontawesome.com/icons)

### Example Mockups
- [Configuration Tab - Integrated V2](./vantage-configuration-tab-integrated-v2.html)

---

## Questions or Issues?

- **For mockup technical issues:** Check this guide or review existing mockup source
- **For design questions:** Consult the qomponents library or UX team
- **For GitHub Pages issues:** See the Troubleshooting section above
- **For CRAB ticket questions:** Use the Jira CLI (`acli jira workitem view CRAB-#####`)

---

**Created by:** Emily Robey (with Claude Code)
**Last Updated:** 2025-01-12
**Related Epic:** CRAB-50387
