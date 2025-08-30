# Agile Portfolio Management Dashboard for Market Risk

## Project Overview

This project demonstrates a comprehensive **Agile Portfolio Management Dashboard** designed specifically for Market Risk transformation initiatives in the banking sector, following **Scaled Agile Framework (SAFe)** principles. The dashboard enables portfolio managers to track epics, manage Agile Release Trains (ARTs), plan Program Increments (PIs), and monitor delivery execution.

## Business Context

In the current banking environment, market risk departments are undergoing significant technology transformations driven by:
- **Regulatory Requirements**: Basel III, FRTB, CCAR, SA-CCR compliance
- **Digital Transformation**: Real-time risk calculations, automated stress testing
- **ESG Integration**: Climate risk and sustainability factors
- **Operational Efficiency**: Streamlined processes and enhanced governance

This dashboard addresses the challenges faced by **Lean Portfolio Managers** in coordinating multiple Agile Release Trains while ensuring alignment with strategic business objectives and regulatory mandates.

## Key Features

### 🎯 **Epic Prioritization & Management**
- Interactive epic board with drag-and-drop functionality
- Business value vs. effort analysis (value/complexity matrix)
- ROI-based prioritization with regulatory driver tracking
- Epic ownership and value stream alignment

### 🚂 **Agile Release Train (ART) Performance**  
- Real-time capacity utilization monitoring
- Velocity tracking across program increments
- Team allocation and workload distribution
- RTE (Release Train Engineer) performance metrics

### 📅 **Program Increment (PI) Planning Support**
- PI planning event coordination and tracking
- Dependencies visualization and management
- Risk identification and mitigation planning
- Team confidence voting ("fist of five") results

### ⚠️ **Risk & Issue Management**
- Portfolio-level risk heatmap
- Issue escalation and tracking
- Regulatory compliance monitoring
- Trend analysis and predictive insights

### 📊 **Portfolio Sync & Governance**
- Executive dashboard with key performance indicators
- Stakeholder engagement tracking
- Budget and resource utilization metrics
- Delivery predictability measurements

## Technical Architecture

### Frontend
- **HTML5** with semantic structure
- **CSS3** with custom properties and responsive design
- **Vanilla JavaScript** with modern ES6+ features
- **Chart.js** for data visualization

### Data Management
- **JSON-based** data storage (easily replaceable with API integration)
- **CSV data import/export** capabilities
- **Local storage** for user preferences and settings

### Integration Ready
- **REST API** endpoints prepared for JIRA integration
- **Webhook support** for real-time updates
- **Export capabilities** for PowerBI/Tableau integration

## Sample Data

The project includes realistic sample data representing a Market Risk transformation portfolio:

- **5 Market Risk Epics**: VaR calculations, stress testing, counterparty risk, data quality, ESG integration
- **3 Agile Release Trains**: Market Risk Core, Risk Analytics Engine, Counterparty Solutions  
- **22 Features**: Decomposed epic components with story point estimates
- **Portfolio KPIs**: Health metrics, delivery predictability, resource utilization
- **PI Planning History**: Past planning sessions with confidence scores and dependencies

## Installation & Setup

```bash
# Clone the repository
git clone https://github.com/your-username/agile-portfolio-dashboard.git
cd agile-portfolio-dashboard

# Start local development server
python -m http.server 8000
# OR
npx live-server

# Open in browser
open http://localhost:8000
```

## File Structure

```
agile-portfolio-dashboard/
├── README.md                          # This file
├── index.html                         # Main application HTML
├── style.css                          # Application styles
├── app.js                             # Main JavaScript application
├── data/
│   ├── market_risk_epics.csv         # Epic data
│   ├── agile_release_trains.csv      # ART performance data
│   ├── features_backlog.csv          # Feature breakdown
│   ├── pi_planning_history.csv       # Planning session data
│   ├── stakeholders.csv              # Key stakeholder info
│   └── portfolio_kpis.json           # KPI metrics
├── docs/
│   ├── SAFE_IMPLEMENTATION_GUIDE.md  # SAFe principles and implementation
│   ├── API_INTEGRATION_GUIDE.md      # Integration with external systems
│   └── USER_GUIDE.md                 # End-user documentation
├── scripts/
│   ├── data_generator.py             # Sample data generation
│   └── export_tools.py               # Data export utilities
└── assets/
    ├── screenshots/                   # Application screenshots
    └── templates/                     # Report templates
```

## Usage Examples

### Epic Prioritization
```javascript
// Prioritize epics by business value and regulatory impact
const prioritizedEpics = epics
  .filter(epic => epic.regulatoryDriver === 'Basel III')
  .sort((a, b) => b.businessValue - a.businessValue);
```

### ART Capacity Management
```javascript
// Calculate ART utilization across all trains
const avgUtilization = arts.reduce((sum, art) => 
  sum + art.utilization, 0) / arts.length;
```

### PI Planning Metrics
```javascript
// Track planning effectiveness
const planningEffectiveness = {
  confidenceScore: piPlanning.confidenceScore,
  dependenciesResolved: piPlanning.dependenciesResolved,
  risksIdentified: piPlanning.risksIdentified
};
```

## SAFe Alignment

This dashboard implements core SAFe practices:

- **Lean Portfolio Management**: Strategy-to-execution alignment
- **Value Stream Coordination**: Cross-functional delivery optimization  
- **PI Planning**: Cadence-based planning and commitment
- **Continuous Delivery Pipeline**: Flow metrics and predictability
- **Inspect & Adapt**: Regular retrospectives and improvements

## Regulatory Considerations

The dashboard specifically addresses market risk regulatory requirements:

- **Basel III**: Capital adequacy and risk management
- **FRTB**: Fundamental Review of the Trading Book
- **CCAR**: Comprehensive Capital Analysis and Review
- **SA-CCR**: Standardised Approach for Counterparty Credit Risk
- **ESG**: Environmental, Social, and Governance risk factors

## Customization Guide

### Adding New Epics
1. Update `data/market_risk_epics.csv` with new epic details
2. Ensure proper value stream assignment
3. Set appropriate business value and effort estimates
4. Define regulatory drivers and expected ROI

### Configuring ARTs
1. Modify `data/agile_release_trains.csv` for your organization
2. Update team counts and capacity metrics
3. Assign Release Train Engineers (RTEs)
4. Map to appropriate value streams

### KPI Customization
1. Edit `data/portfolio_kpis.json` to reflect your metrics
2. Update dashboard calculations in `app.js`
3. Modify chart configurations for new KPIs
4. Add threshold alerts for critical metrics

## Integration Options

### JIRA Integration
```javascript
// Example API call to fetch epics from JIRA
async function fetchEpicsFromJira() {
  const response = await fetch('/api/jira/epics', {
    headers: { 'Authorization': 'Bearer ' + jiraToken }
  });
  return response.json();
}
```

### Microsoft Project Integration
- Export PI plans to MS Project format
- Import resource allocation data
- Sync milestone and dependency information

### PowerBI/Tableau Integration
- Data export in compatible formats
- Automated refresh capabilities
- Custom report generation

## Performance Metrics

The dashboard tracks key SAFe metrics:
- **Flow Metrics**: Lead time, cycle time, throughput
- **Quality Metrics**: Defect rates, technical debt
- **Predictability**: Plan vs. actual delivery
- **Employee Engagement**: Team confidence scores

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on:
- Code standards and review process
- Feature request procedures  
- Bug reporting and resolution
- Documentation improvements

## Roadmap

### Phase 2 Features
- [ ] Mobile application for on-the-go access
- [ ] Advanced analytics and machine learning insights
- [ ] Integration with popular enterprise tools (ServiceNow, Azure DevOps)
- [ ] Multi-portfolio view for enterprise-level management

### Phase 3 Enhancements
- [ ] Real-time collaboration features
- [ ] Advanced dependency mapping
- [ ] Predictive risk modeling
- [ ] Automated compliance reporting

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Support

For questions, issues, or contributions:
- Create an issue on GitHub
- Contact the project maintainers
- Review the documentation in the `/docs` folder

## Acknowledgments

- **Scaled Agile Framework (SAFe)** methodology and principles
- **Lean Portfolio Management** best practices
- **Market Risk** domain expertise and regulatory guidance
- Open source community for tools and libraries used

---

**Built with ❤️ for Agile Portfolio Managers in Banking & Finance**