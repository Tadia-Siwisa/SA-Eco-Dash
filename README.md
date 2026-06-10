<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>South Africa Economy Dashboard</title>
    <meta property="og:title" content="South Africa Economy Dashboard 2026">
    <meta property="og:description" content="Interactive data visualization of South Africa's economic landscape — financial markets, inequality, education, technology, and more.">
    <meta property="og:type" content="website">
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800;900&family=Space+Grotesk:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --bg-primary: #0a0a0f;
            --bg-secondary: #111118;
            --bg-card: #16161f;
            --bg-hover: #1e1e2a;
            --text-primary: #f0f0f5;
            --text-secondary: #8a8a9a;
            --text-muted: #5a5a6a;
            --accent-financial: #00d4aa;
            --accent-inequality: #ff4757;
            --accent-food: #ffa502;
            --accent-education: #7bed9f;
            --accent-tech: #70a1ff;
            --accent-crime: #ff6b81;
            --accent-water: #5352ed;
            --border: #2a2a3a;
            --shadow: 0 8px 32px rgba(0,0,0,0.4);
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        html, body {
            height: 100%;
            overflow: hidden;
            font-family: 'Inter', sans-serif;
            background: var(--bg-primary);
            color: var(--text-primary);
        }

        .app-container {
            height: 100vh;
            width: 100vw;
            position: relative;
            overflow: hidden;
        }

        /* ===== HUB PAGE ===== */
        .hub-page {
            position: absolute;
            inset: 0;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            padding: 2rem;
            transition: transform 0.6s cubic-bezier(0.4, 0, 0.2, 1), opacity 0.4s ease;
            z-index: 10;
        }

        .hub-page.hidden {
            transform: scale(0.9);
            opacity: 0;
            pointer-events: none;
        }

        .hub-header {
            text-align: center;
            margin-bottom: 3rem;
        }

        .hub-header h1 {
            font-family: 'Space Grotesk', sans-serif;
            font-size: clamp(2rem, 5vw, 3.5rem);
            font-weight: 700;
            letter-spacing: -0.02em;
            margin-bottom: 0.5rem;
            background: linear-gradient(135deg, #fff 0%, #8a8a9a 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .hub-header .subtitle {
            font-size: 1rem;
            color: var(--text-secondary);
            font-weight: 400;
        }

        .hub-header .date-badge {
            display: inline-block;
            margin-top: 1rem;
            padding: 0.4rem 1rem;
            background: var(--bg-card);
            border: 1px solid var(--border);
            border-radius: 100px;
            font-size: 0.75rem;
            color: var(--text-muted);
            letter-spacing: 0.05em;
        }

        .hub-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(260px, 1fr));
            gap: 1rem;
            width: 100%;
            max-width: 900px;
        }

        .hub-card {
            background: var(--bg-card);
            border: 1px solid var(--border);
            border-radius: 16px;
            padding: 1.5rem;
            cursor: pointer;
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            position: relative;
            overflow: hidden;
        }

        .hub-card::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            height: 3px;
            background: var(--card-accent);
            opacity: 0.7;
            transition: opacity 0.3s;
        }

        .hub-card:hover {
            transform: translateY(-4px);
            border-color: var(--card-accent);
            box-shadow: 0 12px 40px rgba(0,0,0,0.3), 0 0 20px var(--card-accent-glow);
        }

        .hub-card:hover::before {
            opacity: 1;
        }

        .hub-card .card-icon {
            width: 44px;
            height: 44px;
            border-radius: 12px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.4rem;
            margin-bottom: 1rem;
            background: var(--card-accent-bg);
        }

        .hub-card h3 {
            font-family: 'Space Grotesk', sans-serif;
            font-size: 1.1rem;
            font-weight: 600;
            margin-bottom: 0.4rem;
        }

        .hub-card p {
            font-size: 0.8rem;
            color: var(--text-secondary);
            line-height: 1.5;
        }

        .hub-card .card-stat {
            margin-top: 1rem;
            font-size: 1.5rem;
            font-weight: 700;
            font-family: 'Space Grotesk', sans-serif;
            color: var(--card-accent);
        }

        /* ===== SLIDE SYSTEM ===== */
        .slides-container {
            position: absolute;
            inset: 0;
            display: flex;
            transition: transform 0.6s cubic-bezier(0.4, 0, 0.2, 1);
        }

        .slide {
            min-width: 100vw;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            padding: 0;
            position: relative;
            overflow: hidden;
        }

        /* Slide Header */
        .slide-header {
            padding: 1.5rem 2rem;
            display: flex;
            align-items: center;
            justify-content: space-between;
            border-bottom: 1px solid var(--border);
            background: rgba(10, 10, 15, 0.8);
            backdrop-filter: blur(20px);
            position: sticky;
            top: 0;
            z-index: 100;
        }

        .slide-header .back-btn {
            display: flex;
            align-items: center;
            gap: 0.5rem;
            padding: 0.6rem 1.2rem;
            background: var(--bg-card);
            border: 1px solid var(--border);
            border-radius: 10px;
            color: var(--text-secondary);
            font-size: 0.85rem;
            cursor: pointer;
            transition: all 0.2s;
            font-family: 'Inter', sans-serif;
        }

        .slide-header .back-btn:hover {
            background: var(--bg-hover);
            color: var(--text-primary);
        }

        .slide-header .slide-title {
            font-family: 'Space Grotesk', sans-serif;
            font-size: 1.1rem;
            font-weight: 600;
        }

        .slide-header .slide-nav {
            display: flex;
            align-items: center;
            gap: 0.5rem;
        }

        .slide-header .nav-dot {
            width: 8px;
            height: 8px;
            border-radius: 50%;
            background: var(--border);
            cursor: pointer;
            transition: all 0.2s;
        }

        .slide-header .nav-dot.active {
            background: var(--slide-accent);
            box-shadow: 0 0 8px var(--slide-accent);
        }

        .slide-header .nav-arrow {
            width: 36px;
            height: 36px;
            border-radius: 10px;
            border: 1px solid var(--border);
            background: var(--bg-card);
            color: var(--text-secondary);
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: all 0.2s;
            font-size: 1.2rem;
        }

        .slide-header .nav-arrow:hover {
            background: var(--bg-hover);
            color: var(--text-primary);
        }

        .slide-header .nav-arrow.disabled {
            opacity: 0.3;
            cursor: not-allowed;
        }

        /* Slide Content */
        .slide-content {
            flex: 1;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            padding: 2rem;
            overflow-y: auto;
        }

        .slide-content-inner {
            width: 100%;
            max-width: 800px;
        }

        .slide-accent-bar {
            width: 60px;
            height: 4px;
            border-radius: 2px;
            background: var(--slide-accent);
            margin-bottom: 2rem;
            box-shadow: 0 0 12px var(--slide-accent-glow);
        }

        .slide-content h2 {
            font-family: 'Space Grotesk', sans-serif;
            font-size: clamp(2rem, 4vw, 3rem);
            font-weight: 700;
            margin-bottom: 1rem;
            letter-spacing: -0.02em;
        }

        .slide-content .slide-description {
            font-size: 1.1rem;
            color: var(--text-secondary);
            line-height: 1.7;
            margin-bottom: 3rem;
            max-width: 600px;
        }

        /* Metrics Grid */
        .metrics-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
            gap: 1.2rem;
            width: 100%;
        }

        .metric-card {
            background: var(--bg-card);
            border: 1px solid var(--border);
            border-radius: 14px;
            padding: 1.5rem;
            transition: all 0.3s;
            position: relative;
            overflow: hidden;
        }

        .metric-card:hover {
            border-color: var(--slide-accent);
            transform: translateY(-2px);
        }

        .metric-card .metric-label {
            font-size: 0.75rem;
            text-transform: uppercase;
            letter-spacing: 0.08em;
            color: var(--text-muted);
            margin-bottom: 0.8rem;
            font-weight: 500;
        }

        .metric-card .metric-value {
            font-family: 'Space Grotesk', sans-serif;
            font-size: 2.2rem;
            font-weight: 700;
            color: var(--slide-accent);
            line-height: 1;
            margin-bottom: 0.5rem;
        }

        .metric-card .metric-context {
            font-size: 0.85rem;
            color: var(--text-secondary);
            line-height: 1.5;
        }

        .metric-card .metric-trend {
            display: inline-flex;
            align-items: center;
            gap: 0.3rem;
            margin-top: 0.8rem;
            padding: 0.3rem 0.8rem;
            border-radius: 100px;
            font-size: 0.75rem;
            font-weight: 600;
            background: var(--trend-bg);
            color: var(--trend-color);
        }

        /* Big Number Slide */
        .big-number-container {
            text-align: center;
            padding: 2rem 0;
        }

        .big-number {
            font-family: 'Space Grotesk', sans-serif;
            font-size: clamp(4rem, 12vw, 8rem);
            font-weight: 800;
            color: var(--slide-accent);
            line-height: 1;
            text-shadow: 0 0 40px var(--slide-accent-glow);
            margin-bottom: 1rem;
        }

        .big-number-label {
            font-size: 1.2rem;
            color: var(--text-secondary);
            max-width: 500px;
            margin: 0 auto;
            line-height: 1.6;
        }

        /* Progress Bar */
        .progress-bar-container {
            width: 100%;
            max-width: 600px;
            margin: 1.5rem 0;
        }

        .progress-bar-label {
            display: flex;
            justify-content: space-between;
            margin-bottom: 0.5rem;
            font-size: 0.9rem;
        }

        .progress-bar-track {
            width: 100%;
            height: 8px;
            background: var(--bg-card);
            border-radius: 4px;
            overflow: hidden;
        }

        .progress-bar-fill {
            height: 100%;
            border-radius: 4px;
            background: var(--slide-accent);
            transition: width 1s cubic-bezier(0.4, 0, 0.2, 1);
            box-shadow: 0 0 10px var(--slide-accent-glow);
        }

        /* Swipe Indicator */
        .swipe-indicator {
            position: absolute;
            bottom: 2rem;
            left: 50%;
            transform: translateX(-50%);
            display: flex;
            align-items: center;
            gap: 0.5rem;
            color: var(--text-muted);
            font-size: 0.8rem;
            animation: bounce 2s infinite;
        }

        @keyframes bounce {
            0%, 100% { transform: translateX(-50%) translateY(0); }
            50% { transform: translateX(-50%) translateY(-8px); }
        }

        /* Animations */
        .fade-in-up {
            animation: fadeInUp 0.6s ease forwards;
            opacity: 0;
        }

        @keyframes fadeInUp {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .delay-1 { animation-delay: 0.1s; }
        .delay-2 { animation-delay: 0.2s; }
        .delay-3 { animation-delay: 0.3s; }
        .delay-4 { animation-delay: 0.4s; }

        /* Mobile Optimizations */
        @media (max-width: 768px) {
            .hub-grid {
                grid-template-columns: 1fr;
                max-width: 400px;
            }

            .slide-header {
                padding: 1rem;
            }

            .slide-header .slide-title {
                font-size: 0.9rem;
            }

            .metrics-grid {
                grid-template-columns: 1fr;
            }

            .metric-card .metric-value {
                font-size: 1.8rem;
            }

            .slide-content {
                padding: 1rem;
            }
        }

        /* Touch swipe areas */
        .swipe-zone {
            position: absolute;
            top: 80px;
            bottom: 0;
            width: 15%;
            z-index: 50;
            cursor: pointer;
        }

        .swipe-zone.left { left: 0; }
        .swipe-zone.right { right: 0; }

        @media (min-width: 769px) {
            .swipe-zone { display: none; }
        }
    </style>
</head>
<body>
    <div class="app-container">
        <!-- HUB PAGE -->
        <div class="hub-page" id="hubPage">
            <div class="hub-header">
                <h1>South Africa Economy</h1>
                <p class="subtitle">Key metrics and structural insights</p>
                <div class="date-badge">JUNE 2026</div>
            </div>

            <div class="hub-grid">
                <div class="hub-card" data-slide="0" style="--card-accent: var(--accent-financial); --card-accent-glow: rgba(0,212,170,0.2); --card-accent-bg: rgba(0,212,170,0.1);">
                    <div class="card-icon">📈</div>
                    <h3>Financial Markets</h3>
                    <p>Africa's largest bourse, top 20 globally</p>
                    <div class="card-stat">4.9% GDP</div>
                </div>

                <div class="hub-card" data-slide="1" style="--card-accent: var(--accent-inequality); --card-accent-glow: rgba(255,71,87,0.2); --card-accent-bg: rgba(255,71,87,0.1);">
                    <div class="card-icon">⚠️</div>
                    <h3>Structural Inequality</h3>
                    <p>The core problem — world's highest Gini</p>
                    <div class="card-stat">0.63 Gini</div>
                </div>

                <div class="hub-card" data-slide="2" style="--card-accent: var(--accent-food); --card-accent-glow: rgba(255,165,2,0.2); --card-accent-bg: rgba(255,165,2,0.1);">
                    <div class="card-icon">🌾</div>
                    <h3>Food Security</h3>
                    <p>Net exporter, acute domestic insecurity</p>
                    <div class="card-stat">27% stunting</div>
                </div>

                <div class="hub-card" data-slide="3" style="--card-accent: var(--accent-education); --card-accent-glow: rgba(123,237,159,0.2); --card-accent-bg: rgba(123,237,159,0.1);">
                    <div class="card-icon">🎓</div>
                    <h3>Education</h3>
                    <p>World-class universities, weak foundations</p>
                    <div class="card-stat">7.1 yrs avg</div>
                </div>

                <div class="hub-card" data-slide="4" style="--card-accent: var(--accent-tech); --card-accent-glow: rgba(112,161,255,0.2); --card-accent-bg: rgba(112,161,255,0.1);">
                    <div class="card-icon">💻</div>
                    <h3>Technology & ICT</h3>
                    <p>Africa's most developed tech ecosystem</p>
                    <div class="card-stat">~8% GDP</div>
                </div>

                <div class="hub-card" data-slide="5" style="--card-accent: var(--accent-crime); --card-accent-glow: rgba(255,107,129,0.2); --card-accent-bg: rgba(255,107,129,0.1);">
                    <div class="card-icon">🚨</div>
                    <h3>Crime & Water</h3>
                    <p>Severe violent crime, high water scarcity</p>
                    <div class="card-stat">High risk</div>
                </div>
            </div>
        </div>

        <!-- SLIDES CONTAINER -->
        <div class="slides-container" id="slidesContainer" style="transform: translateX(100%);">

            <!-- SLIDE 0: Financial Markets -->
            <div class="slide" data-index="0" style="--slide-accent: var(--accent-financial); --slide-accent-glow: rgba(0,212,170,0.3);">
                <div class="slide-header">
                    <button class="back-btn" onclick="goToHub()">← Back to Hub</button>
                    <div class="slide-title" style="color: var(--accent-financial);">Financial Markets</div>
                    <div class="slide-nav">
                        <div class="nav-dot active" onclick="goToSlide(0)"></div>
                        <div class="nav-dot" onclick="goToSlide(1)"></div>
                        <div class="nav-dot" onclick="goToSlide(2)"></div>
                        <div class="nav-dot" onclick="goToSlide(3)"></div>
                        <div class="nav-dot" onclick="goToSlide(4)"></div>
                        <div class="nav-dot" onclick="goToSlide(5)"></div>
                        <button class="nav-arrow" onclick="nextSlide()">→</button>
                    </div>
                </div>
                <div class="slide-content">
                    <div class="slide-content-inner">
                        <div class="slide-accent-bar"></div>
                        <h2 class="fade-in-up">Africa's Largest Bourse</h2>
                        <p class="slide-description fade-in-up delay-1">South Africa's financial sector is structurally one of its strongest assets — independent central bank, sophisticated capital markets, globally credible.</p>

                        <div class="metrics-grid">
                            <div class="metric-card fade-in-up delay-2">
                                <div class="metric-label">JSE All Share Index</div>
                                <div class="metric-value">~83,500</div>
                                <div class="metric-context">June 2026</div>
                            </div>
                            <div class="metric-card fade-in-up delay-3">
                                <div class="metric-label">GDP Contribution</div>
                                <div class="metric-value">4.9%</div>
                                <div class="metric-context">Of total GDP</div>
                            </div>
                            <div class="metric-card fade-in-up delay-4">
                                <div class="metric-label">Global Ranking</div>
                                <div class="metric-value">Top 20</div>
                                <div class="metric-context">Stock exchanges worldwide</div>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="swipe-indicator">← Swipe to navigate →</div>
                <div class="swipe-zone left" onclick="prevSlide()"></div>
                <div class="swipe-zone right" onclick="nextSlide()"></div>
            </div>

            <!-- SLIDE 1: Structural Inequality -->
            <div class="slide" data-index="1" style="--slide-accent: var(--accent-inequality); --slide-accent-glow: rgba(255,71,87,0.3);">
                <div class="slide-header">
                    <button class="back-btn" onclick="goToHub()">← Back to Hub</button>
                    <div class="slide-title" style="color: var(--accent-inequality);">Structural Inequality</div>
                    <div class="slide-nav">
                        <button class="nav-arrow" onclick="prevSlide()">←</button>
                        <div class="nav-dot" onclick="goToSlide(0)"></div>
                        <div class="nav-dot active" onclick="goToSlide(1)"></div>
                        <div class="nav-dot" onclick="goToSlide(2)"></div>
                        <div class="nav-dot" onclick="goToSlide(3)"></div>
                        <div class="nav-dot" onclick="goToSlide(4)"></div>
                        <div class="nav-dot" onclick="goToSlide(5)"></div>
                        <button class="nav-arrow" onclick="nextSlide()">→</button>
                    </div>
                </div>
                <div class="slide-content">
                    <div class="slide-content-inner">
                        <div class="slide-accent-bar"></div>
                        <h2 class="fade-in-up">The Core Problem</h2>
                        <p class="slide-description fade-in-up delay-1">With a Gini of 0.63, income is concentrated in a narrow top tier while the majority subsist on welfare transfers. Real GDP per capita is below 2007 levels — a lost generation of growth.</p>

                        <div class="big-number-container fade-in-up delay-2">
                            <div class="big-number">0.63</div>
                            <div class="big-number-label">World's highest Gini coefficient<br>Direct legacy of apartheid dispossession</div>
                        </div>

                        <div class="metrics-grid" style="margin-top: 2rem;">
                            <div class="metric-card fade-in-up delay-3">
                                <div class="metric-label">Upper Poverty Line</div>
                                <div class="metric-value">55.5%</div>
                                <div class="metric-context">~35 million people below upper-middle-income poverty line (2024)</div>
                                <div class="metric-trend" style="--trend-bg: rgba(255,71,87,0.15); --trend-color: #ff4757;">⚠️ Critical</div>
                            </div>
                            <div class="metric-card fade-in-up delay-4">
                                <div class="metric-label">Extreme Poverty</div>
                                <div class="metric-value">21.5%</div>
                                <div class="metric-context">~13.7 million people below $2.15/day</div>
                                <div class="metric-trend" style="--trend-bg: rgba(255,71,87,0.15); --trend-color: #ff4757;">⚠️ Critical</div>
                            </div>
                        </div>

                        <div style="margin-top: 2rem;">
                            <div class="progress-bar-container fade-in-up delay-3">
                                <div class="progress-bar-label">
                                    <span>Expanded Unemployment (incl. discouraged)</span>
                                    <span style="color: var(--accent-inequality); font-weight: 700;">~42%</span>
                                </div>
                                <div class="progress-bar-track">
                                    <div class="progress-bar-fill" style="width: 42%;"></div>
                                </div>
                            </div>
                            <div class="progress-bar-container fade-in-up delay-4">
                                <div class="progress-bar-label">
                                    <span>Below Upper Poverty Line</span>
                                    <span style="color: var(--accent-inequality); font-weight: 700;">55.5%</span>
                                </div>
                                <div class="progress-bar-track">
                                    <div class="progress-bar-fill" style="width: 55.5%;"></div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="swipe-indicator">← Swipe to navigate →</div>
                <div class="swipe-zone left" onclick="prevSlide()"></div>
                <div class="swipe-zone right" onclick="nextSlide()"></div>
            </div>

            <!-- SLIDE 2: Food Security -->
            <div class="slide" data-index="2" style="--slide-accent: var(--accent-food); --slide-accent-glow: rgba(255,165,2,0.3);">
                <div class="slide-header">
                    <button class="back-btn" onclick="goToHub()">← Back to Hub</button>
                    <div class="slide-title" style="color: var(--accent-food);">Food Security</div>
                    <div class="slide-nav">
                        <button class="nav-arrow" onclick="prevSlide()">←</button>
                        <div class="nav-dot" onclick="goToSlide(0)"></div>
                        <div class="nav-dot" onclick="goToSlide(1)"></div>
                        <div class="nav-dot active" onclick="goToSlide(2)"></div>
                        <div class="nav-dot" onclick="goToSlide(3)"></div>
                        <div class="nav-dot" onclick="goToSlide(4)"></div>
                        <div class="nav-dot" onclick="goToSlide(5)"></div>
                        <button class="nav-arrow" onclick="nextSlide()">→</button>
                    </div>
                </div>
                <div class="slide-content">
                    <div class="slide-content-inner">
                        <div class="slide-accent-bar"></div>
                        <h2 class="fade-in-up">Stressed</h2>
                        <p class="slide-description fade-in-up delay-1">Net food exporter — but domestic food insecurity is acute. Food insecurity is driven by purchasing power gaps, not production deficits. High food inflation disproportionately hits low-income households.</p>

                        <div class="metrics-grid">
                            <div class="metric-card fade-in-up delay-2">
                                <div class="metric-label">Child Stunting</div>
                                <div class="metric-value">~27%</div>
                                <div class="metric-context">Children under 5 face stunting from malnutrition</div>
                                <div class="metric-trend" style="--trend-bg: rgba(255,165,2,0.15); --trend-color: #ffa502;">⚠️ High</div>
                            </div>
                            <div class="metric-card fade-in-up delay-3">
                                <div class="metric-label">Arable Land</div>
                                <div class="metric-value">~12%</div>
                                <div class="metric-context">Of total land area</div>
                            </div>
                            <div class="metric-card fade-in-up delay-4">
                                <div class="metric-label">Irrigated Land</div>
                                <div class="metric-value">~1.5M</div>
                                <div class="metric-context">Hectares under irrigation</div>
                            </div>
                        </div>

                        <div style="margin-top: 2rem; padding: 1.5rem; background: var(--bg-card); border-radius: 14px; border: 1px solid var(--border);" class="fade-in-up delay-3">
                            <div style="font-size: 0.75rem; text-transform: uppercase; letter-spacing: 0.08em; color: var(--text-muted); margin-bottom: 1rem; font-weight: 500;">Agricultural Heartlands</div>
                            <div style="display: flex; flex-wrap: wrap; gap: 0.8rem;">
                                <span style="padding: 0.5rem 1rem; background: rgba(255,165,2,0.1); border: 1px solid rgba(255,165,2,0.3); border-radius: 8px; font-size: 0.85rem;">Western Cape (fruit/wine)</span>
                                <span style="padding: 0.5rem 1rem; background: rgba(255,165,2,0.1); border: 1px solid rgba(255,165,2,0.3); border-radius: 8px; font-size: 0.85rem;">Free State (grain)</span>
                                <span style="padding: 0.5rem 1rem; background: rgba(255,165,2,0.1); border: 1px solid rgba(255,165,2,0.3); border-radius: 8px; font-size: 0.85rem;">KZN (sugar)</span>
                                <span style="padding: 0.5rem 1rem; background: rgba(255,165,2,0.1); border: 1px solid rgba(255,165,2,0.3); border-radius: 8px; font-size: 0.85rem;">Northern Cape (livestock)</span>
                            </div>
                            <p style="margin-top: 1rem; font-size: 0.85rem; color: var(--text-secondary);">Land reform remains deeply contested.</p>
                        </div>
                    </div>
                </div>
                <div class="swipe-indicator">← Swipe to navigate →</div>
                <div class="swipe-zone left" onclick="prevSlide()"></div>
                <div class="swipe-zone right" onclick="nextSlide()"></div>
            </div>

            <!-- SLIDE 3: Education -->
            <div class="slide" data-index="3" style="--slide-accent: var(--accent-education); --slide-accent-glow: rgba(123,237,159,0.3);">
                <div class="slide-header">
                    <button class="back-btn" onclick="goToHub()">← Back to Hub</button>
                    <div class="slide-title" style="color: var(--accent-education);">Education</div>
                    <div class="slide-nav">
                        <button class="nav-arrow" onclick="prevSlide()">←</button>
                        <div class="nav-dot" onclick="goToSlide(0)"></div>
                        <div class="nav-dot" onclick="goToSlide(1)"></div>
                        <div class="nav-dot" onclick="goToSlide(2)"></div>
                        <div class="nav-dot active" onclick="goToSlide(3)"></div>
                        <div class="nav-dot" onclick="goToSlide(4)"></div>
                        <div class="nav-dot" onclick="goToSlide(5)"></div>
                        <button class="nav-arrow" onclick="nextSlide()">→</button>
                    </div>
                </div>
                <div class="slide-content">
                    <div class="slide-content-inner">
                        <div class="slide-accent-bar"></div>
                        <h2 class="fade-in-up">The Skills Gap is Structural</h2>
                        <p class="slide-description fade-in-up delay-1">UCT, Wits, Stellenbosch are globally competitive. But foundational schooling outcomes — numeracy, literacy — rank among the worst of all middle-income countries.</p>

                        <div class="metrics-grid">
                            <div class="metric-card fade-in-up delay-2">
                                <div class="metric-label">Average Schooling</div>
                                <div class="metric-value">7.1</div>
                                <div class="metric-context">Years completed</div>
                            </div>
                            <div class="metric-card fade-in-up delay-3">
                                <div class="metric-label">Expected Schooling</div>
                                <div class="metric-value">13.7</div>
                                <div class="metric-context">Years if enrolled on time</div>
                            </div>
                            <div class="metric-card fade-in-up delay-4">
                                <div class="metric-label">Matric Pass Rate</div>
                                <div class="metric-value">~80%</div>
                                <div class="metric-context">2025 examination results</div>
                                <div class="metric-trend" style="--trend-bg: rgba(123,237,159,0.15); --trend-color: #7bed9f;">↑ Improving</div>
                            </div>
                            <div class="metric-card fade-in-up delay-2">
                                <div class="metric-label">Tertiary Enrollment</div>
                                <div class="metric-value">~1.1M</div>
                                <div class="metric-context">Students in higher education</div>
                            </div>
                            <div class="metric-card fade-in-up delay-3">
                                <div class="metric-label">Public Universities</div>
                                <div class="metric-value">26</div>
                                <div class="metric-context">Institutions nationwide</div>
                            </div>
                            <div class="metric-card fade-in-up delay-4">
                                <div class="metric-label">Adult Literacy</div>
                                <div class="metric-value">~95%</div>
                                <div class="metric-context">Population aged 15+</div>
                            </div>
                        </div>

                        <div style="margin-top: 2rem; padding: 1.5rem; background: rgba(123,237,159,0.05); border-radius: 14px; border: 1px solid rgba(123,237,159,0.2);" class="fade-in-up delay-3">
                            <div style="display: flex; align-items: center; gap: 1rem;">
                                <div style="font-size: 2rem;">📉</div>
                                <div>
                                    <div style="font-weight: 600; margin-bottom: 0.3rem;">PISA Numeracy Rank: Bottom Tier</div>
                                    <div style="font-size: 0.85rem; color: var(--text-secondary);">Despite high literacy rates, South Africa ranks among the worst middle-income countries for foundational numeracy and literacy skills.</div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="swipe-indicator">← Swipe to navigate →</div>
                <div class="swipe-zone left" onclick="prevSlide()"></div>
                <div class="swipe-zone right" onclick="nextSlide()"></div>
            </div>

            <!-- SLIDE 4: Technology & ICT -->
            <div class="slide" data-index="4" style="--slide-accent: var(--accent-tech); --slide-accent-glow: rgba(112,161,255,0.3);">
                <div class="slide-header">
                    <button class="back-btn" onclick="goToHub()">← Back to Hub</button>
                    <div class="slide-title" style="color: var(--accent-tech);">Technology & ICT</div>
                    <div class="slide-nav">
                        <button class="nav-arrow" onclick="prevSlide()">←</button>
                        <div class="nav-dot" onclick="goToSlide(0)"></div>
                        <div class="nav-dot" onclick="goToSlide(1)"></div>
                        <div class="nav-dot" onclick="goToSlide(2)"></div>
                        <div class="nav-dot" onclick="goToSlide(3)"></div>
                        <div class="nav-dot active" onclick="goToSlide(4)"></div>
                        <div class="nav-dot" onclick="goToSlide(5)"></div>
                        <button class="nav-arrow" onclick="nextSlide()">→</button>
                    </div>
                </div>
                <div class="slide-content">
                    <div class="slide-content-inner">
                        <div class="slide-accent-bar"></div>
                        <h2 class="fade-in-up">Growing</h2>
                        <p class="slide-description fade-in-up delay-1">Africa's most developed tech ecosystem outside Egypt/Nigeria. Cape Town is emerging as Africa's tech capital. SA produces more software patents per capita than any other African nation.</p>

                        <div class="metrics-grid">
                            <div class="metric-card fade-in-up delay-2">
                                <div class="metric-label">Internet Penetration</div>
                                <div class="metric-value">~72%</div>
                                <div class="metric-context">Of population online</div>
                                <div class="metric-trend" style="--trend-bg: rgba(112,161,255,0.15); --trend-color: #70a1ff;">↑ Growing</div>
                            </div>
                            <div class="metric-card fade-in-up delay-3">
                                <div class="metric-label">Mobile SIMs</div>
                                <div class="metric-value">>100M</div>
                                <div class="metric-context">Active subscriptions</div>
                            </div>
                            <div class="metric-card fade-in-up delay-4">
                                <div class="metric-label">ICT Share of GDP</div>
                                <div class="metric-value">~8%</div>
                                <div class="metric-context">And growing rapidly</div>
                            </div>
                        </div>

                        <div style="margin-top: 2rem; padding: 1.5rem; background: var(--bg-card); border-radius: 14px; border: 1px solid var(--border);" class="fade-in-up delay-3">
                            <div style="font-size: 0.75rem; text-transform: uppercase; letter-spacing: 0.08em; color: var(--text-muted); margin-bottom: 1rem; font-weight: 500;">Key Growth Sectors</div>
                            <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(150px, 1fr)); gap: 1rem;">
                                <div style="text-align: center; padding: 1rem;">
                                    <div style="font-size: 2rem; margin-bottom: 0.5rem;">🏢</div>
                                    <div style="font-weight: 600; font-size: 0.9rem;">BPO</div>
                                    <div style="font-size: 0.75rem; color: var(--text-secondary);">Business Process Outsourcing</div>
                                </div>
                                <div style="text-align: center; padding: 1rem;">
                                    <div style="font-size: 2rem; margin-bottom: 0.5rem;">💳</div>
                                    <div style="font-weight: 600; font-size: 0.9rem;">Fintech</div>
                                    <div style="font-size: 0.75rem; color: var(--text-secondary);">Financial technology</div>
                                </div>
                                <div style="text-align: center; padding: 1rem;">
                                    <div style="font-size: 2rem; margin-bottom: 0.5rem;">🤖</div>
                                    <div style="font-weight: 600; font-size: 0.9rem;">AI</div>
                                    <div style="font-size: 0.75rem; color: var(--text-secondary);">Artificial Intelligence</div>
                                </div>
                            </div>
                            <p style="margin-top: 1rem; font-size: 0.85rem; color: var(--text-secondary); text-align: center;">Growing rapidly since energy stabilisation</p>
                        </div>

                        <div style="margin-top: 1.5rem; display: flex; gap: 1rem; flex-wrap: wrap; justify-content: center;" class="fade-in-up delay-4">
                            <span style="padding: 0.6rem 1.2rem; background: rgba(112,161,255,0.1); border: 1px solid rgba(112,161,255,0.3); border-radius: 100px; font-size: 0.85rem;">📍 Tech Hubs: JHB · CPT</span>
                            <span style="padding: 0.6rem 1.2rem; background: rgba(112,161,255,0.1); border: 1px solid rgba(112,161,255,0.3); border-radius: 100px; font-size: 0.85rem;">🏆 Most software patents per capita in Africa</span>
                        </div>
                    </div>
                </div>
                <div class="swipe-indicator">← Swipe to navigate →</div>
                <div class="swipe-zone left" onclick="prevSlide()"></div>
                <div class="swipe-zone right" onclick="nextSlide()"></div>
            </div>

            <!-- SLIDE 5: Crime & Water -->
            <div class="slide" data-index="5" style="--slide-accent: var(--accent-crime); --slide-accent-glow: rgba(255,107,129,0.3);">
                <div class="slide-header">
                    <button class="back-btn" onclick="goToHub()">← Back to Hub</button>
                    <div class="slide-title" style="color: var(--accent-crime);">Crime & Water</div>
                    <div class="slide-nav">
                        <button class="nav-arrow" onclick="prevSlide()">←</button>
                        <div class="nav-dot" onclick="goToSlide(0)"></div>
                        <div class="nav-dot" onclick="goToSlide(1)"></div>
                        <div class="nav-dot" onclick="goToSlide(2)"></div>
                        <div class="nav-dot" onclick="goToSlide(3)"></div>
                        <div class="nav-dot" onclick="goToSlide(4)"></div>
                        <div class="nav-dot active" onclick="goToSlide(5)"></div>
                        <button class="nav-arrow disabled" onclick="nextSlide()">→</button>
                    </div>
                </div>
                <div class="slide-content">
                    <div class="slide-content-inner">
                        <div class="slide-accent-bar"></div>
                        <h2 class="fade-in-up">Severe Challenges</h2>
                        <p class="slide-description fade-in-up delay-1">Consistently among world's highest violent crime rates. Water scarcity risk is high across key agricultural regions.</p>

                        <div class="metrics-grid">
                            <div class="metric-card fade-in-up delay-2" style="border-color: var(--accent-crime);">
                                <div class="metric-label">Violent Crime</div>
                                <div class="metric-value" style="color: var(--accent-crime);">Severe</div>
                                <div class="metric-context">Consistently among the world's highest violent crime rates</div>
                                <div class="metric-trend" style="--trend-bg: rgba(255,107,129,0.15); --trend-color: #ff6b81;">⚠️ Critical</div>
                            </div>
                            <div class="metric-card fade-in-up delay-3" style="border-color: var(--accent-water);">
                                <div class="metric-label">Water Scarcity Risk</div>
                                <div class="metric-value" style="color: var(--accent-water);">High</div>
                                <div class="metric-context">Especially in Western Cape, Free State, KZN, Northern Cape</div>
                                <div class="metric-trend" style="--trend-bg: rgba(83,82,237,0.15); --trend-color: #5352ed;">⚠️ High Risk</div>
                            </div>
                        </div>

                        <div style="margin-top: 2rem; padding: 2rem; background: var(--bg-card); border-radius: 14px; border: 1px solid var(--border); text-align: center;" class="fade-in-up delay-4">
                            <div style="font-size: 3rem; margin-bottom: 1rem;">🌍</div>
                            <h3 style="font-family: 'Space Grotesk', sans-serif; font-size: 1.3rem; margin-bottom: 0.5rem;">The South African Paradox</h3>
                            <p style="color: var(--text-secondary); font-size: 0.95rem; line-height: 1.7; max-width: 600px; margin: 0 auto;">
                                South Africa possesses the institutional and infrastructural capabilities of an upper-middle-income country, 
                                but the distribution of opportunity remains trapped in an apartheid-era pattern. Without addressing structural 
                                inequality, even strong financial and tech sectors may not translate into broad-based prosperity.
                            </p>
                            <div style="margin-top: 1.5rem; padding-top: 1.5rem; border-top: 1px solid var(--border);">
                                <div style="font-size: 0.75rem; text-transform: uppercase; letter-spacing: 0.08em; color: var(--text-muted); margin-bottom: 0.5rem;">Real GDP Per Capita</div>
                                <div style="font-family: 'Space Grotesk', sans-serif; font-size: 1.5rem; font-weight: 700; color: var(--accent-inequality);">Below 2007 Levels</div>
                                <div style="font-size: 0.85rem; color: var(--text-secondary); margin-top: 0.3rem;">A lost generation of growth</div>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="swipe-indicator">← Swipe to navigate</div>
                <div class="swipe-zone left" onclick="prevSlide()"></div>
            </div>
        </div>
    </div>

    <script>
        let currentSlide = -1;
        const totalSlides = 6;
        let touchStartX = 0;
        let touchEndX = 0;

        // Hub card clicks
        document.querySelectorAll('.hub-card').forEach(card => {
            card.addEventListener('click', () => {
                const slideIndex = parseInt(card.dataset.slide);
                goToSlide(slideIndex);
            });
        });

        function goToHub() {
            currentSlide = -1;
            document.getElementById('hubPage').classList.remove('hidden');
            document.getElementById('slidesContainer').style.transform = 'translateX(100%)';
            document.body.style.overflow = 'hidden';
        }

        function goToSlide(index) {
            if (index < 0 || index >= totalSlides) return;
            currentSlide = index;

            document.getElementById('hubPage').classList.add('hidden');
            document.getElementById('slidesContainer').style.transform = `translateX(-${index * 100}%)`;

            updateNavDots();
            updateNavArrows();

            // Reset animations
            document.querySelectorAll('.slide[data-index="' + index + '"] .fade-in-up').forEach(el => {
                el.style.animation = 'none';
                el.offsetHeight; // trigger reflow
                el.style.animation = '';
            });
        }

        function nextSlide() {
            if (currentSlide < totalSlides - 1) {
                goToSlide(currentSlide + 1);
            }
        }

        function prevSlide() {
            if (currentSlide > 0) {
                goToSlide(currentSlide - 1);
            } else if (currentSlide === 0) {
                goToHub();
            }
        }

        function updateNavDots() {
            document.querySelectorAll('.nav-dot').forEach((dot, i) => {
                dot.classList.toggle('active', i === currentSlide);
            });
        }

        function updateNavArrows() {
            document.querySelectorAll('.slide .nav-arrow').forEach(arrow => {
                // This is handled per-slide in the HTML via onclick, but we could add disabled states here
            });
        }

        // Keyboard navigation
        document.addEventListener('keydown', (e) => {
            if (currentSlide === -1) {
                if (e.key === 'ArrowRight' || e.key === 'Enter') {
                    goToSlide(0);
                }
                return;
            }

            if (e.key === 'ArrowRight') nextSlide();
            if (e.key === 'ArrowLeft') prevSlide();
            if (e.key === 'Escape') goToHub();
        });

        // Touch swipe support
        document.addEventListener('touchstart', (e) => {
            touchStartX = e.changedTouches[0].screenX;
        }, { passive: true });

        document.addEventListener('touchend', (e) => {
            touchEndX = e.changedTouches[0].screenX;
            handleSwipe();
        }, { passive: true });

        function handleSwipe() {
            const swipeThreshold = 50;
            const diff = touchStartX - touchEndX;

            if (Math.abs(diff) > swipeThreshold) {
                if (diff > 0) {
                    nextSlide();
                } else {
                    prevSlide();
                }
            }
        }

        // Mouse wheel navigation (optional)
        let wheelTimeout;
        document.addEventListener('wheel', (e) => {
            if (currentSlide === -1) return;

            clearTimeout(wheelTimeout);
            wheelTimeout = setTimeout(() => {
                if (e.deltaY > 50) nextSlide();
                if (e.deltaY < -50) prevSlide();
            }, 50);
        }, { passive: true });

        // Initialize
        goToHub();
    </script>
</body>
</html>
