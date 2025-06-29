# Pyodide + Plotly: Two Approaches Compared

[![Status](https://img.shields.io/badge/Status-Ready-brightgreen?style=for-the-badge)](https://github.com/BlockSecCA/pyodide-plotly-shootout)
[![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white)](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/HTML5)
[![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
[![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![Pyodide](https://img.shields.io/badge/Pyodide-FFD43B?style=for-the-badge&logo=python&logoColor=white)](https://pyodide.org/)
[![Plotly.js](https://img.shields.io/badge/Plotly.js-238C6F?style=for-the-badge&logo=plotly&logoColor=white)](https://plotly.com/javascript/)


This project demonstrates two different architectural approaches for creating data visualizations using Python in the browser via Pyodide and Plotly.

## 🎯 The Problem

How do you create sophisticated data visualizations in a browser environment using Python without backend infrastructure? Two main approaches emerge:

1. **Full Python Stack**: Python handles both computation AND visualization
2. **Hybrid Approach**: Python for computation, JavaScript for visualization

## 📁 Files in this Project

- `pyodide-plotly-sankey-demo.html` - **Approach A**: Full Python stack
- `pyodide-js-plotly-demo.html` - **Approach B**: Hybrid approach
- `README.md` - This documentation

## 🏗️ Approach A: Full Python Stack

**File**: `pyodide-plotly-sankey-demo.html`

### Architecture
```
Browser → Pyodide → Python Plotly → HTML/JS Rendering
```

### How it Works
1. Loads Pyodide (Python runtime for browser)
2. Installs Python Plotly via micropip
3. Python code creates Plotly charts using `plotly.graph_objects`
4. Python generates complete chart configuration as JSON
5. JavaScript receives the chart config and renders with Plotly.js

### Code Flow Example
```python
# Python handles everything
import plotly.graph_objects as go

# Create chart in Python
fig = go.Figure(data=go.Sankey(...))
chart_json = fig.to_json()  # Complete chart config
```

```javascript
// JavaScript just renders
const chartConfig = JSON.parse(pythonChartJson);
Plotly.newPlot('div', chartConfig.data, chartConfig.layout);
```

### Pros
- ✅ Pure Python workflow - familiar for Python developers
- ✅ Complete Plotly Python API available
- ✅ Complex chart configurations handled in Python

### Cons
- ❌ **Reliability issues**: Pyodide + Python Plotly integration can be fragile
- ❌ **Larger bundle**: Must load full Python Plotly package (~several MB)
- ❌ **Slower startup**: More packages to install via micropip
- ❌ **Debugging complexity**: Errors can occur in Python Plotly → JSON → JS chain

### Best For
- Projects where Python Plotly's advanced features are essential
- Teams with strong Python expertise but limited JavaScript knowledge
- Complex statistical plots that leverage Python Plotly's specialized functions

## 🚀 Approach B: Hybrid Python + JavaScript

**File**: `pyodide-js-plotly-demo.html`

### Architecture
```
Browser → Pyodide → Python (data processing) → JSON → JavaScript Plotly → Rendering
```

### How it Works
1. Loads Pyodide with minimal packages (numpy, basic scientific libraries)
2. Python performs data processing, analysis, and computations
3. Python returns clean, structured JSON data
4. JavaScript receives data and creates Plotly visualizations directly
5. Native Plotly.js handles all rendering and interactivity

### Code Flow Example
```python
# Python focuses on computation
import numpy as np

def analyze_data():
    # Complex data processing here
    x_data = np.random.normal(0, 1, 1000)
    y_data = x_data * 2 + np.random.normal(0, 0.5, 1000)
    
    return {
        'x': x_data.tolist(),
        'y': y_data.tolist(),
        'categories': ['A', 'B', 'C'] * 333 + ['A']
    }

result = analyze_data()
json.dumps(result)  # Clean data structure
```

```javascript
// JavaScript handles visualization
const data = JSON.parse(pythonResult);
const plotData = [{
    x: data.x,
    y: data.y,
    type: 'scatter',
    mode: 'markers'
}];
Plotly.newPlot('chart', plotData, layout);
```

### Pros
- ✅ **Higher reliability**: No complex Python Plotly integration
- ✅ **Faster loading**: Minimal Python dependencies
- ✅ **Better performance**: Native JavaScript Plotly is optimized for browsers
- ✅ **Easier debugging**: Clear separation between computation and visualization
- ✅ **Flexible**: Can easily switch visualization libraries if needed

### Cons
- ❌ Requires JavaScript knowledge for visualization code
- ❌ Must manually translate between Python data structures and Plotly.js configs
- ❌ Can't leverage some advanced Python Plotly features directly

### Best For
- **Most production use cases** - more reliable and maintainable
- Teams comfortable with both Python and JavaScript
- Applications where visualization performance matters
- Projects that might need to switch visualization libraries later

## 🔍 Detailed Comparison

| Aspect | Approach A (Full Python) | Approach B (Hybrid) |
|--------|-------------------------|----------------------|
| **Reliability** | ⚠️ Moderate - Integration complexity | ✅ High - Simple, proven pattern |
| **Bundle Size** | ❌ Large (~10-15MB total) | ✅ Smaller (~5-8MB total) |
| **Startup Time** | ❌ Slower (Python Plotly install) | ✅ Faster (minimal packages) |
| **Python Complexity** | ✅ Simple (familiar Plotly API) | ⚠️ Moderate (data structure design) |
| **JavaScript Complexity** | ✅ Minimal (just rendering) | ⚠️ Moderate (chart configuration) |
| **Performance** | ⚠️ Good | ✅ Excellent |
| **Debugging** | ❌ Complex (multi-layer) | ✅ Easy (clear boundaries) |
| **Flexibility** | ⚠️ Locked to Python Plotly | ✅ Can change viz libraries |

## 🎛️ When to Use Each Approach

### Choose Approach A (Full Python) when:
- Your team is Python-heavy with limited JavaScript experience
- You need specific Python Plotly features not easily replicated
- You're prototyping and want familiar Python syntax
- The project is small-scale and reliability isn't critical

### Choose Approach B (Hybrid) when:
- You want maximum reliability and performance
- The project might scale or go to production
- Your team has JavaScript capability
- You value maintainability and debugging simplicity
- You might need to change visualization libraries in the future

## 🛠️ Development Experience Notes

### Common Issues with Approach A
- Pyodide + Python Plotly loading order problems
- JSON serialization edge cases
- Version compatibility between Pyodide and Python Plotly
- Difficult to debug when charts don't render

### Common Issues with Approach B
- Designing clean data structures for complex visualizations
- Translating Python analysis results to Plotly.js configurations
- Managing state between Python computations and JavaScript rendering

## 💡 "El-Cheapo" Pattern Validation

Both approaches validate the core "El-Cheapo" concept:
- **No backend servers required** - everything runs client-side
- **Single HTML file deployment** - can be hosted anywhere
- **Sophisticated visualizations** - comparable to full-stack solutions
- **Cost-effective** - no ongoing infrastructure costs

**Recommendation**: Start with **Approach B (Hybrid)** for most projects. It offers the best balance of reliability, performance, and maintainability while still achieving the cost-effectiveness goals of the El-Cheapo pattern.

## 🚀 Getting Started

1. **Download either HTML file**
2. **Open in a modern browser** (Chrome, Firefox, Safari, Edge)
3. **Wait for Pyodide to load** (30-60 seconds first time)
4. **Click buttons to generate visualizations**
5. **Inspect the code** to understand the patterns

No installation, no servers, no configuration - just open and run!

## Beyond Client-Side: When Pyodide Visualizations May Not Be Suitable

While Pyodide offers powerful client-side Python execution, there are scenarios where a purely browser-based Python visualization approach may not be suitable, especially when Python is required for data processing:

-   **Non-Web-Native GUI Toolkits**: If your Python data processing leads to visualizations exclusively rendered by desktop GUI libraries (e.g., some specialized scientific plotting tools built on PyQt, Tkinter, etc.) that lack web-based counterparts or export options, direct browser display is not feasible.
-   **Extreme Performance or Low-Level Browser API Access**: For cutting-edge, highly specialized, or extremely performance-sensitive interactive visualizations that demand direct, low-level manipulation of WebGL/WebGPU contexts or very tight, synchronous loops with browser events, pure JavaScript/TypeScript and WebAssembly (not necessarily Python-in-WebAssembly) might be required for maximum control and minimal overhead.
-   **Dependencies on Uncompiled Native C Extensions**: If your Python data processing relies on specific Python packages with critical C extensions that have not been compiled for WebAssembly and included in Pyodide, that part of your Python code cannot run in the browser, preventing a fully client-side visualization workflow.
-   **Heavy Server-Side Computation or External Resource Access**: Visualizations that inherently require continuous, heavy server-side Python computation (e.g., very long-running, CPU-intensive calculations that would block the browser's UI thread) or constant interaction with external resources (e.g., massive databases, real-time data streams, proprietary APIs) that cannot be accessed directly from the browser (due to security policies like CORS or performance limitations) will necessitate server-side Python processing, with only the processed results sent to the browser for visualization.

## 📚 Further Reading

- [Pyodide Documentation](https://pyodide.org/)
- [Plotly.js Documentation](https://plotly.com/javascript/)
- [Python Plotly Documentation](https://plotly.com/python/)
