---
name: chartjs-radar
description: The `chartjs-radar` component utilizes the Chart.js library to render responsive radar charts, offering both guided and advanced data input modes, customizable legend settings, and dynamic updates, while emitting events on user interaction.
keywords:
  - radar chart
  - chart.js
  - multivariate data
  - data binding
  - guided mode
  - advanced mode
  - chart legend
  - dataset configuration
  - responsive chart
  - data aggregation
---

#### chartjs-radar

Component Purpose: Renders a radar chart using Chart.js library for displaying multivariate data in a two-dimensional chart.

Properties:
- isLegend: boolean - Display chart legend. Default: true
- legendPosition: 'top' | 'bottom' | 'left' | 'right' - Legend position. Default: "top"
- legendAlignement: 'start' | 'center' | 'end' - Legend alignment. Default: "center"
- legendSize: number - Legend font size in pixels. Default: 12
- legendColor: string | null - Legend label color. Default: null
- dataType: string - Sets the configuration mode. **CRITICAL** Always set to "advanced".
- labels: binded<string[]> - X-axis labels. advanced mode only.
- datasets: binded<object[]> - Dataset objects with label, backgroundColor, borderColor, data keys. advanced mode only.
- options: binded<object> - Advanced chart options. advanced mode only.
- xAxisTitle: string - Category labels field name (guided mode)
- dataXField: string - Category data field path (guided mode). Default: null
- dataXFieldProperty: string - Nested category data path (guided mode). Default: null
- dataOrderBy: 'default' | 'x' | 'y' - Data ordering field (guided mode). Default: "default"
- dataDirection: 'ASC' | 'DESC' - Sort direction (guided mode). Default: "ASC"
- dataXEmpty: boolean - Include empty categories (guided mode). Default: false
- yAxis: 'item-count' | 'field-summary' - Y-axis value type (guided mode). Default: "item-count"
- dataYField: string - Value data field path (guided mode). Default: null
- dataYFieldProperty: string - Nested value data path (guided mode). Default: null
- aggregate: 'distinct' | 'value' | 'sum' | 'average' | 'median' | 'min' | 'max' - Aggregation function (guided mode). Default: "distinct"
- colors: string[] - Dataset line colors (guided mode). Default: []

Children: none

Note: Set max-width: 100% and height: auto for responsive graphs. Set min-width: 0px on parent container.

Events:
- chart:click
  Payload: {position: {x: number, y: number}, points: [{label: string, value: number, index: number, datasetIndex: number}]}
  Description: Triggered when user clicks on the chart, providing click position and data points information

Variables: none

Note: 
- To make graph responsive: First, always set these options : responsive: true and maintainAspectRatio: false, Second, set min-width: 0px to direct parent container.
- **IMPORTANT** labels, datasets, options and data properties HAVE TO BE BINDED data