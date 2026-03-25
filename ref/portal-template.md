# Portal Update Instructions

## How to update the portal with new report data

The portal at `~/.local/share/lab-rat/portal/index.html` loads data from an inline
`<script>` block, NOT from fetch(). This is because `file://` protocol blocks CORS.

### Steps to update portal data:

1. Read `~/.local/share/lab-rat/reports.json` to get current reports array
2. Add/update the report entry
3. Write updated reports.json back
4. Read `~/.local/share/lab-rat/portal/index.html`
5. Find the script block containing `window.REPORTS_DATA = `
6. Replace the entire array value with the current reports.json content
7. Write the updated HTML back

### Script block format:
```html
<script>
  window.REPORTS_DATA = [
    // ... report entries here
  ];
</script>
```

### Important:
- Always preserve the rest of the HTML — only replace the REPORTS_DATA value
- The reports.json file is the source of truth; the HTML inline data is a copy
- Both must stay in sync
- Use JSON.stringify with 2-space indent for readability
