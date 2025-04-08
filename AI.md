---
name: chartjs-radar
description: Renders responsive radar charts using Chart.js library for displaying multivariate data with customizable legend and data input modes.
keywords: [radar chart, chart.js, multivariate data, data binding, guided mode, advanced mode, chart legend, dataset configuration, responsive chart, data aggregation]
---

#### chartjs-radar

***Purpose:***
Renders a radar chart using Chart.js library for displaying multivariate data in a two-dimensional chart, offering  customizable legend settings and dynamic updates.

***Features:***
- Supports both guided and advanced data input modes
- Customizable legend settings (position, alignment, size, color)
- Responsive design with dynamic updates
- Data aggregation capabilities in guided mode
- Interactive chart with click events

***Properties:***
- dataType: string - Sets the configuration mode. **CRITICAL** Always set to "advanced"
- labels: binded<string[]> - X-axis labels.
- datasets: binded<object[]> - Dataset objects with label, backgroundColor, borderColor, data keys.
- options: binded<object> - Advanced chart options.
- isLegend: boolean - Display chart legend. Default: true
- legendPosition: 'top'|'bottom'|'left'|'right' - Legend position. Default: "top"
- legendAlignement: 'start'|'center'|'end' - Legend alignment. Default: "center"
- legendSize: number - Legend font size in pixels. Default: 12
- legendColor: string|null - Legend label color. Default: null
- xAxisTitle: string - Category labels field name (guided mode)
- dataXField: string - Category data field path (guided mode). Default: null
- dataXFieldProperty: string - Nested category data path (guided mode). Default: null
- dataOrderBy: 'default'|'x'|'y' - Data ordering field (guided mode). Default: "default"
- dataDirection: 'ASC'|'DESC' - Sort direction (guided mode). Default: "ASC"
- dataXEmpty: boolean - Include empty categories (guided mode). Default: false
- yAxis: 'item-count'|'field-summary' - Y-axis value type (guided mode). Default: "item-count"
- dataYField: string - Value data field path (guided mode). Default: null
- dataYFieldProperty: string - Nested value data path (guided mode). Default: null
- aggregate: 'distinct'|'value'|'sum'|'average'|'median'|'min'|'max' - Aggregation function (guided mode). Default: "distinct"
- colors: string[] - Dataset line colors (guided mode). Default: []

***Events:***
- chart:click: Triggered when user clicks on the chart. Payload: {position: {x: number, y: number}, points: [{label: string, value: number, index: number, datasetIndex: number}]}

***Notes:***
- ALWAYS MAKE IT RESPONSIVE: Set these options: responsive: true and maintainAspectRatio: false, PLUS, set min-width: 0px to direct parent container.
- **IMPORTANT**: labels, datasets, options and data properties HAVE TO BE BINDED data, using {"js":"..."}
- Set max-width: 100% and height: auto for responsive graphs