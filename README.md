# 🕍 Shtetl - Many Shuls. One Community.

<div align="center">

![Shtetl Logo](https://img.shields.io/badge/Shtetl-Community_Platform-4A90E2?style=for-the-badge&logo=data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iMTYiIGhlaWdodD0iMTYiIHZpZXdOb3g9IjAgMCAxNiAxNiIgZmlsbD0ibm9uZSIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIj4KPHBhdGggZD0iTTggMkw2IDZINCw4SDEyTDEwIDZIOFoiIHN0cm9rZT0id2hpdGUiIHN0cm9rZS13aWR0aD0iMSIgZmlsbD0id2hpdGUiLz4KPC9zdmc+)
![License](https://img.shields.io/badge/License-Open_Source-green?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-In_Development-orange?style=for-the-badge)
![Community](https://img.shields.io/badge/Community-Driven-purple?style=for-the-badge)

**🌟 The Digital Heartbeat of Jewish Life 🌟**

*Unifying Jewish community communications into a centralized, intelligent hub*

[🚀 Get Started](#-quick-start) • [📖 Documentation](#-documentation) • [🤝 Contributing](#-contributing) • [💬 Community](#-community)

</div>

---

## 🎯 What is Shtetl?

**Shtetl** is a free, open-source platform that transforms fragmented Jewish community communications into a unified, intelligent ecosystem. No more juggling WhatsApp groups, printed bulletins, and endless phone calls about minyan times!

### 🌈 Key Features

<table>
<tr>
<td width="50%">

#### 🕐 **Smart Scheduling**
- ✅ Real-time zmanim calculations
- ✅ Automated minyan reminders
- ✅ Dynamic shiur schedules
- ✅ Holiday & special event handling

</td>
<td width="50%">

#### 📱 **Multi-Channel Access**
- ✅ Native mobile apps (iOS/Android)
- ✅ SMS alerts & notifications
- ✅ Web admin console
- ✅ API integrations (MCP/Zapier)

</td>
</tr>
<tr>
<td width="50%">

#### 🏛️ **Shul Management**
- ✅ Membership tracking
- ✅ Aliyos scheduling
- ✅ Yahrzeit reminders
- ✅ Simcha coordination

</td>
<td width="50%">

#### 🤖 **Smart Automation**
- ✅ Facility control (HVAC/Lighting)
- ✅ Personalized wake-up alarms
- ✅ Location-aware notifications
- ✅ Event-triggered actions

</td>
</tr>
</table>

---

## 🎨 Core Value Proposition

<div align="center">

### **"Find Your Minyan. Never Miss A Shiur. Stay Connected."**

</div>

```mermaid
graph LR
    A[👥 Shul Admins] -->|Manage| B[📊 Shtetl Platform]
    B --> C[📱 Mobile Apps]
    B --> D[💬 SMS/Email]
    B --> E[🏠 Smart Building]
    B --> F[🔌 API/Webhooks]

    C --> G[👨‍👩‍👧‍👦 Community Members]
    D --> G
    E --> G
    F --> G

    style B fill:#4A90E2,stroke:#333,stroke-width:2px,color:#fff
    style G fill:#90EE90,stroke:#333,stroke-width:2px
```

---

## 🚀 Quick Start

...

---

## 🏗️ Architecture

<div align="center">

```
┌─────────────────────────────────────────────────────────┐
│                    🌐 Web Admin Console                 │
│                     (React + TypeScript)                │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│                   🚪 API Gateway                        │
│                  (Golang + GraphQL)                     │
└────────┬──────────────────────────────┬─────────────────┘
         │                              │
┌────────▼────────┐            ┌───────▼────────┐
│  📱 Mobile Apps │            │ 🔌 Integrations │
│  (React Native) │            │  (MCP/Zapier)   │
└─────────────────┘            └─────────────────┘
         │                              │
┌────────▼──────────────────────────────▼─────────────────┐
│              💾 Core Services (Microservices)           │
│  • Schedule Service  • Alert Service  • Auth Service    │
│  • Automation Service • Zmanim Service • Event Service  │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│                 🗄️ Data Layer                           │
│         MongoDB (Documents) + Redis (Cache)             │
└──────────────────────────────────────────────────────────┘
```

</div>

---

## 📊 Project Status & Roadmap

### 🎯 Current Phase: **Research & Planning**

<div align="center">

| Phase | Status | Target Date |
|-------|--------|-------------|
| 🔍 **Research & Planning** | ✅ Complete | Q3 2025 |
| 🏗️ **MVP Development** | 🟡 In Progress | Q1 2026 |
| 🧪 **Beta Testing** | ⏳ Planned | Q3 2026 |
| 🚀 **Public Launch** | ⏳ Planned | Q4 2026 |

</div>

### 🎁 MVP Features (Q1 2026)
- [ ] Shul admin portals
- [ ] Minyan scheduling
- [ ] Mobile app (iOS/Android)
- [ ] SMS/Email notifications
- [ ] Zmanim calculations

### 🚀 Post-MVP Vision (Q1-Q4 2027)
- [ ] Complete membership management
- [ ] Aliyos & yahrzeit tracking
- [ ] Public APIs
- [ ] Multi-language support (Hebrew/Yiddish)
- [ ] Advanced automation features
- [ ] Cross-Shul discovery

---

## 🤝 Contributing

We welcome contributions from developers, designers, and community members!

### 🛠️ How to Contribute

1. **Fork the repository**
2. **Create your feature branch** (`git checkout -b feature/AmazingFeature`)
3. **Commit your changes** (`git commit -m 'Add some AmazingFeature'`)
4. **Push to the branch** (`git push origin feature/AmazingFeature`)
5. **Open a Pull Request**

### 🎨 Areas We Need Help

<table>
<tr>
<td>

**💻 Leadership**

- Technical Leadership
- Project Management
- Architecture & Design
- PR Reviews

</td>
<td>
&nbsp;

**💻 Development**

- Frontend (React/React Native)
- Backend (Golang)
- Mobile development
- Business Analysis
- QA

&nbsp;
</td>
<td>

**🎨 Design**

- UI/UX design
- Mobile app design
- Logo & branding
- Documentation

</td>
<td>

**📝 Community**

- Testing & feedback
- Translation
- Community outreach
- Design Partners

</td>
</tr>
</table>

---

## 💬 Community

Join our growing community of contributors and users!

<div align="center">

[![Slack](https://img.shields.io/badge/Slack-Join_Workspace-4A154B?style=for-the-badge&logo=slack&logoColor=white)](#)
[![GitHub Discussions](https://img.shields.io/badge/GitHub-Discussions-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/jcom-dev/shtetl/discussions)

</div>

### 🌟 Success Metrics

<div align="center">

| Metric | Target | Current |
|--------|--------|---------|
| 🏛️ **Active Shuls** | 15 | 0 |
| 👥 **Active Users** | 1,000+ | 0 |
| 📱 **Daily Active Users** | 70% | - |

</div>

---

## 📖 Documentation

- 📋 [Project Brief](docs/brief.md) - Comprehensive project overview
- 🏗️ [Architecture Guide](docs/architecture/README.md) - Technical architecture details
- 🚀 [Getting Started](docs/getting-started.md) - Quick start guide
- 📚 [API Documentation](docs/api/README.md) - API reference
- 🤝 [Contributing Guide](CONTRIBUTING.md) - How to contribute

---

## 🛡️ Technical Stack

<div align="center">

![React](https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB)
![React Native](https://img.shields.io/badge/React_Native-20232A?style=for-the-badge&logo=react&logoColor=61DAFB)
![Go](https://img.shields.io/badge/Go-00ADD8?style=for-the-badge&logo=go&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-4EA94B?style=for-the-badge&logo=mongodb&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?style=for-the-badge&logo=redis&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazon-aws&logoColor=white)
![GraphQL](https://img.shields.io/badge/GraphQL-E10098?style=for-the-badge&logo=graphql&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)

</div>

---

## 📜 License

This project is open source and available under the [GPL License](LICENSE).

---

## 🙏 Acknowledgments

- All our amazing contributors and community members
- The Shuls partnering with us as design partners
- Open source projects that make this possible

---

<div align="center">

### **🌟 Together, we're building the future of Jewish community connectivity 🌟**

**Made with ❤️ by the Jewish tech community**

[⬆ Back to top](#-shtetl---unite-your-jewish-community)

</div>