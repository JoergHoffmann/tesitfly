<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>IT-Sicherheits-Check DIN SPEC 27076</title>
    <!-- Tailwind CSS via CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- React & Babel via CDN für Standalone-Betrieb -->
    <script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800;900&display=swap');
        body { font-family: 'Inter', sans-serif; }
        .scrollbar-hide::-webkit-scrollbar { display: none; }
        @media print {
            .no-print { display: none; }
        }
    </style>
</head>
<body class="bg-slate-50">
    <div id="root"></div>

    <script type="text/babel">
        const { useState, useMemo } = React;

        // --- Robuste Inline SVG Icons ---
        const Icon = ({ name, className = "w-5 h-5" }) => {
            const icons = {
                ShieldCheck: <path d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" />,
                Users: <><path d="M16 21v-2a4 4 0 0 0-4-4H6a4 4 0 0 0-4 4v2" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /><circle cx="9" cy="7" r="4" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /><path d="M22 21v-2a4 4 0 0 0-3-3.87" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /><path d="M16 3.13a4 4 0 0 1 0 7.75" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /></>,
                Key: <><path d="m21 2-2 2" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /><circle cx="7.5" cy="15.5" r="5.5" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /><path d="m21 2-9.6 9.6" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /><path d="m15.5 7.5 3 3" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /></>,
                AlertTriangle: <><path d="m21.73 18-8-14a2 2 0 0 0-3.48 0l-8 14A2 2 0 0 0 4 21h16a2 2 0 0 0 1.73-3Z" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /><path d="M12 9v4" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /><path d="M12 17h.01" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /></>,
                CheckCircle2: <><circle cx="12" cy="12" r="10" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /><path d="m9 12 2 2 4-4" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /></>,
                ArrowRight: <><path d="M5 12h14" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /><path d="m12 5 7 7-7 7" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /></>,
                ArrowLeft: <><path d="m12 19-7-7 7-7" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /><path d="M19 12H5" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /></>,
                Printer: <><polyline points="6 9 6 2 18 2 18 9" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /><path d="M6 18H4a2 2 0 0 1-2-2v-5a2 2 0 0 1 2-2h16a2 2 0 0 1 2 2v5a2 2 0 0 1-2 2h-2" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /><rect width="12" height="8" x="6" y="14" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /></>,
                HardDrive: <><rect width="20" height="8" x="2" y="14" rx="2" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /><path d="M6 18h.01" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /><path d="M10 18h.01" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /><path d="M2 10V5a2 2 0 0 1 2-2h16a2 2 0 0 1 2 2v5" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /></>,
                RefreshCcw: <><path d="M21 12a9 9 0 0 0-9-9 9.75 9.75 0 0 0-6.74 2.74L3 8" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /><path d="M3 3v5h5" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /><path d="M3 12a9 9 0 0 0 9 9 9.75 9.75 0 0 0 6.74-2.74L21 16" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /><path d="M16 16h5v5" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /></>,
                Globe: <><circle cx="12" cy="12" r="10" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /><path d="M2 12h20" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /><path d="M12 2a15.3 15.3 0 0 1 4 10 15.3 15.3 0 0 1-4 10 15.3 15.3 0 0 1-4-10 15.3 15.3 0 0 1 4-10z" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /></>,
                ClipboardCheck: <><rect width="8" height="4" x="8" y="2" rx="1" ry="1" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /><path d="M16 4h2a2 2 0 0 1 2 2v14a2 2 0 0 1-2 2H6a2 2 0 0 1-2-2V6a2 2 0 0 1 2-2h2" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /><path d="m9 14 2 2 4-4" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" /></>
            };
            return (
                <svg viewBox="0 0 24 24" className={className}>
                    {icons[name] || <circle cx="12" cy="12" r="10" stroke="currentColor" strokeWidth="2" />}
                </svg>
            );
        };

        // Katalog-Daten
        const SECTIONS = [
            {
                id: 'org',
                title: "1. Organisation & Sensibilisierung",
                icon: "Users",
                questions: [
                    { id: '01', text: "Trägt die Geschäftsführung die Gesamtverantwortung für Informationssicherheit?", isTop: true, recommendation: "Die Geschäftsführung muss die Verantwortung übernehmen und das Thema vorleben." },
                    { id: '02', text: "Gibt es eine benannte verantwortliche Person (intern oder extern)?", isTop: false, recommendation: "Ernennen Sie einen IT-Sicherheitsbeauftragten oder beauftragen Sie einen Dienstleister." },
                    { id: '03', text: "Gibt es einen Notfallkontakt für ungewöhnliche Vorkommnisse?", isTop: false, recommendation: "Beschäftigte müssen jederzeit wissen, wen sie bei Problemen oder Geräteverlust kontaktieren." },
                    { id: '04', text: "Gibt es einen Notfallplan, der allen Beschäftigten bekannt ist?", isTop: true, recommendation: "Entwickeln Sie einen Plan für das Verhalten bei IT-Notfällen." },
                    { id: '05', text: "Werden alle Beschäftigten regelmäßig geschult (z.B. Phishing-Erkennung)?", isTop: false, recommendation: "Führen Sie Sensibilisierungsmaßnahmen durch." },
                    { id: '06', text: "Gibt es schriftliche Vertraulichkeitsregelungen?", isTop: false, recommendation: "Formulieren Sie interne Regeln zur Vertraulichkeit im Umgang mit IT." },
                    { id: '07', text: "Gibt es Richtlinien für Homeoffice und mobiles Arbeiten?", isTop: false, recommendation: "Erstellen Sie Sicherheitsmaßnahmen für mobiles Arbeiten und lassen Sie diese bestätigen." }
                ]
            },
            {
                id: 'idm',
                title: "2. Identitäts- & Berechtigungsmanagement",
                icon: "Key",
                questions: [
                    { id: '08', text: "Ist der Zutritt zu Räumen nur für Berechtigte möglich?", isTop: false, recommendation: "Kontrollieren und dokumentieren Sie den Zutritt zu IT-Räumen." },
                    { id: '09', text: "Nutzen Beschäftigte individuelle und komplexe Passwörter?", isTop: false, recommendation: "Erzwingen Sie komplexe Passwörter für jedes Benutzerkonto." },
                    { id: '10', text: "Wird überall dort, wo möglich, eine 2-Faktor-Authentifizierung (2FA) genutzt?", isTop: false, recommendation: "Aktivieren Sie 2FA bei Cloud-Diensten und kritischen Systemen." }
                ]
            },
            {
                id: 'backup',
                title: "3. Datensicherung",
                icon: "HardDrive",
                questions: [
                    { id: '11', text: "Erfolgt eine regelmäßige Datensicherung (mind. wöchentlich)?", isTop: true, recommendation: "Sichern Sie Daten so, dass der Geschäftsbetrieb wiederhergestellt werden kann." },
                    { id: '12', text: "Ist die Sicherung vor unbefugtem Zugriff geschützt (z.B. verschlüsselt)?", isTop: false, recommendation: "Sichern Sie die Backups gegen Diebstahl und unberechtigten Zugriff." },
                    { id: '13', text: "Ist das Backup-Verfahren schriftlich festgelegt?", isTop: false, recommendation: "Verschriftlichen Sie Speicherorte, Häufigkeit und Zuständigkeiten." },
                    { id: '14', text: "Wird die Wiederherstellung regelmäßig getestet?", isTop: false, recommendation: "Prüfen Sie mind. 2x jährlich die Funktionsfähigkeit der Backups." }
                ]
            },
            {
                id: 'patch',
                title: "4. Patch- & Änderungsmanagement",
                icon: "RefreshCcw",
                questions: [
                    { id: '15', text: "Werden Updates für Systeme und Software zeitnah installiert?", isTop: true, recommendation: "Nutzen Sie automatische Updates, um Sicherheitslücken zu schließen." },
                    { id: '16', text: "Gibt es eine verantwortliche Person für das Update-Management?", isTop: false, recommendation: "Bestimmen Sie, wer für die Aktualität der Software zuständig ist." },
                    { id: '17', text: "Werden veraltete Systeme (ohne Sicherheitsupdates) ausgemustert?", isTop: false, recommendation: "Identifizieren und ersetzen Sie Hard-/Software ohne Herstellersupport." }
                ]
            },
            {
                id: 'malware',
                title: "5. Schutz vor Schadprogrammen",
                icon: "ShieldCheck",
                questions: [
                    { id: '18', text: "Verfügen alle Endgeräte über einen aktiven Virenschutz?", isTop: false, recommendation: "Statten Sie Server, PCs und Smartphones mit Schutzsoftware aus." },
                    { id: '19', text: "Darf Software nur von IT-Verantwortlichen installiert werden?", isTop: false, recommendation: "Beschränken Sie die Installationsrechte auf berechtigte Personen." },
                    { id: '20', text: "Sind Makros in Office-Programmen standardmäßig deaktiviert?", isTop: true, recommendation: "Deaktivieren Sie Makros und lassen Sie Aktivierung nur im begründeten Einzelfall zu." }
                ]
            },
            {
                id: 'network',
                title: "6. IT-Systeme & Netzwerke",
                icon: "Globe",
                questions: [
                    { id: '21', text: "Ist eine Firewall installiert und aktiv?", isTop: false, recommendation: "Schützen Sie das Firmennetzwerk durch eine Firewall vor Angriffen." },
                    { id: '22', text: "Ist die Firewall individuell konfiguriert (nur nötige Dienste)?", isTop: false, recommendation: "Lassen Sie die Firewall von Experten konfigurieren." },
                    { id: '23', text: "Sind alle mobilen Geräte (Tablets, Laptops) passwortgeschützt?", isTop: false, recommendation: "Erzwingen Sie Sperrbildschirme mit Passwort/PIN." },
                    { id: '24', text: "Erfolgt der Zugriff von außen nur über verschlüsseltes VPN?", isTop: false, recommendation: "Nutzen Sie VPN für Homeoffice-Zugriffe." },
                    { id: '25', text: "Ist das WLAN nach aktuellen Standards (WPA2/3) verschlüsselt?", isTop: false, recommendation: "Sichern Sie das WLAN mit komplexen Passwörtern (mind. 20 Zeichen)." },
                    { id: '26', text: "Ist Fernwartung nur unter kontrollierten Bedingungen möglich?", isTop: false, recommendation: "Regeln Sie Zeitpunkt und Verschlüsselung von Fernwartungszugriffen." },
                    { id: '27', text: "Sind IT-Komponenten vor Elementarschäden (Feuer, Wasser) geschützt?", isTop: false, recommendation: "Sichern Sie Hardware gegen Hitze, Überspannung und Wasser." }
                ]
            }
        ];

        const RadarChart = ({ stats }) => {
            const size = 300;
            const center = size / 2;
            const radius = 100;
            const points = stats.map((s, i) => {
                const angle = (Math.PI * 2 * i) / stats.length - Math.PI / 2;
                const val = s.max > 0 ? (s.score / s.max) : 0;
                const x = center + radius * val * Math.cos(angle);
                const y = center + radius * val * Math.sin(angle);
                return `${x},${y}`;
            }).join(' ');

            const axisPoints = stats.map((_, i) => {
                const angle = (Math.PI * 2 * i) / stats.length - Math.PI / 2;
                return {
                    x2: center + radius * Math.cos(angle),
                    y2: center + radius * Math.sin(angle)
                };
            });

            return (
                <div className="flex justify-center my-4">
                    <svg width={size} height={size} className="overflow-visible">
                        {[0.2, 0.4, 0.6, 0.8, 1].map(r => (
                            <circle key={r} cx={center} cy={center} r={radius * r} fill="none" stroke="#e2e8f0" strokeWidth="1" />
                        ))}
                        {axisPoints.map((p, i) => (
                            <line key={i} x1={center} y1={center} x2={p.x2} y2={p.y2} stroke="#cbd5e1" strokeWidth="1" />
                        ))}
                        <polygon points={points} fill="rgba(37, 99, 235, 0.2)" stroke="#2563eb" strokeWidth="2" />
                        {stats.map((s, i) => {
                            const angle = (Math.PI * 2 * i) / stats.length - Math.PI / 2;
                            const labelRadius = radius + 35;
                            const x = center + labelRadius * Math.cos(angle);
                            const y = center + labelRadius * Math.sin(angle);
                            return (
                                <text key={i} x={x} y={y} fontSize="9" textAnchor="middle" className="fill-slate-500 font-bold uppercase tracking-tighter">
                                    {s.title.split('. ')[1].substring(0, 10)}
                                </text>
                            );
                        })}
                    </svg>
                </div>
            );
        };

        const App = () => {
            const [activeTab, setActiveTab] = useState('audit');
            const [activeSection, setActiveSection] = useState(0);
            const [responses, setResponses] = useState({});

            const handleResponse = (id, value) => {
                setResponses(prev => ({ ...prev, [id]: value }));
            };

            const auditResults = useMemo(() => {
                let totalScore = 0;
                let maxPossibleScore = 0;
                const sectionStats = SECTIONS.map(s => ({ title: s.title, score: 0, max: 0 }));

                SECTIONS.forEach((section, sIdx) => {
                    section.questions.forEach(q => {
                        const response = responses[q.id];
                        const weight = q.isTop ? 3 : 1;

                        if (response !== 'IR') {
                            maxPossibleScore += q.isTop ? 3 : 1;
                            sectionStats[sIdx].max += q.isTop ? 3 : 1;
                            if (response === 'J') {
                                totalScore += weight;
                                sectionStats[sIdx].score += weight;
                            } else if (response === 'N' && q.isTop) {
                                totalScore -= 3;
                            }
                        }
                    });
                });
                return { 
                    score: Math.max(0, totalScore), 
                    max: maxPossibleScore,
                    percentage: maxPossibleScore > 0 ? Math.round((Math.max(0, totalScore) / maxPossibleScore) * 100) : 0,
                    sectionStats 
                };
            }, [responses]);

            return (
                <div className="min-h-screen pb-20">
                    <header className="bg-white border-b sticky top-0 z-50 no-print">
                        <div className="max-w-6xl mx-auto px-6 h-18 py-4 flex items-center justify-between">
                            <div className="flex items-center gap-3">
                                <div className="bg-blue-600 p-2 rounded-lg text-white">
                                    <Icon name="ShieldCheck" className="w-6 h-6" />
                                </div>
                                <h1 className="font-black text-lg">IT-Check DIN 27076</h1>
                            </div>
                            <nav className="hidden md:flex bg-slate-100 p-1 rounded-xl">
                                {['audit', 'report', 'info'].map(tab => (
                                    <button 
                                        key={tab}
                                        onClick={() => setActiveTab(tab)}
                                        className={`px-5 py-2 rounded-lg text-sm font-bold transition-all ${
                                            activeTab === tab ? 'bg-white shadow-sm text-blue-600' : 'text-slate-500 hover:text-slate-700'
                                        }`}
                                    >
                                        {tab === 'audit' ? 'Fragen' : tab === 'report' ? 'Bericht' : 'Wissen'}
                                    </button>
                                ))}
                            </nav>
                            <button className="p-2 text-slate-400 hover:text-blue-600 transition-colors" onClick={() => window.print()}>
                                <Icon name="Printer" className="w-5 h-5" />
                            </button>
                        </div>
                    </header>

                    <main className="max-w-6xl mx-auto px-6 py-10">
                        {activeTab === 'audit' && (
                            <div className="grid grid-cols-1 lg:grid-cols-12 gap-10">
                                <div className="lg:col-span-4 space-y-4">
                                    <div className="bg-white p-6 rounded-2xl shadow-sm border border-slate-200">
                                        <h3 className="text-xs font-black text-slate-400 uppercase tracking-widest mb-6">Bereiche</h3>
                                        <div className="space-y-2">
                                            {SECTIONS.map((s, idx) => (
                                                <button
                                                    key={s.id}
                                                    onClick={() => setActiveSection(idx)}
                                                    className={`w-full flex items-center gap-3 p-3 rounded-xl transition-all text-left ${
                                                        activeSection === idx ? 'bg-blue-50 text-blue-700 ring-1 ring-blue-100' : 'text-slate-500 hover:bg-slate-50'
                                                    }`}
                                                >
                                                    <span className={`p-2 rounded-lg ${activeSection === idx ? 'bg-blue-600 text-white' : 'bg-slate-100'}`}>
                                                        <Icon name={s.icon} className="w-4 h-4" />
                                                    </span>
                                                    <span className="text-sm font-bold">{s.title.split('. ')[1]}</span>
                                                </button>
                                            ))}
                                        </div>
                                    </div>
                                    <div className="bg-blue-600 p-6 rounded-2xl shadow-xl text-white">
                                        <div className="text-3xl font-black mb-2">{auditResults.percentage}%</div>
                                        <div className="text-xs font-bold opacity-80 uppercase mb-4">Fortschritt</div>
                                        <div className="w-full bg-white/20 h-2 rounded-full overflow-hidden">
                                            <div className="bg-white h-full transition-all duration-500" style={{ width: `${auditResults.percentage}%` }}></div>
                                        </div>
                                    </div>
                                </div>

                                <div className="lg:col-span-8">
                                    <div className="bg-white rounded-3xl shadow-sm border border-slate-200 overflow-hidden">
                                        <div className="p-8 border-b bg-slate-50/50 flex items-center gap-4">
                                            <Icon name={SECTIONS[activeSection].icon} className="w-6 h-6 text-blue-600" />
                                            <h2 className="text-xl font-black">{SECTIONS[activeSection].title}</h2>
                                        </div>
                                        <div className="divide-y divide-slate-50">
                                            {SECTIONS[activeSection].questions.map((q) => (
                                                <div key={q.id} className="p-8 hover:bg-slate-50/30 transition-colors">
                                                    <div className="mb-6">
                                                        {q.isTop && <span className="text-[10px] font-black bg-orange-100 text-orange-600 px-2 py-1 rounded mb-2 inline-flex items-center gap-1"><Icon name="AlertTriangle" className="w-3 h-3" /> TOP ANFORDERUNG</span>}
                                                        <p className="text-slate-800 font-bold text-lg leading-snug">{q.text}</p>
                                                    </div>
                                                    <div className="flex flex-wrap gap-2">
                                                        <button 
                                                            onClick={() => handleResponse(q.id, 'J')} 
                                                            className={`flex-1 min-w-[100px] py-3 rounded-xl font-black text-xs border transition-all ${responses[q.id] === 'J' ? 'bg-green-600 text-white border-green-600 shadow-lg shadow-green-100' : 'bg-white text-slate-500 border-slate-200 hover:border-slate-300'}`}
                                                        >
                                                            JA
                                                        </button>
                                                        <button 
                                                            onClick={() => handleResponse(q.id, 'N')} 
                                                            className={`flex-1 min-w-[100px] py-3 rounded-xl font-black text-xs border transition-all ${responses[q.id] === 'N' ? 'bg-red-600 text-white border-red-600 shadow-lg shadow-red-100' : 'bg-white text-slate-500 border-slate-200 hover:border-slate-300'}`}
                                                        >
                                                            NEIN
                                                        </button>
                                                        <button 
                                                            onClick={() => handleResponse(q.id, 'IR')} 
                                                            className={`flex-1 min-w-[100px] py-3 rounded-xl font-black text-xs border transition-all ${responses[q.id] === 'IR' ? 'bg-slate-400 text-white border-slate-400 shadow-lg shadow-slate-100' : 'bg-white text-slate-500 border-slate-200 hover:border-slate-300'}`}
                                                        >
                                                            NICHT RELEVANT
                                                        </button>
                                                    </div>
                                                </div>
                                            ))}
                                        </div>
                                        <div className="p-8 bg-slate-50 flex justify-between">
                                            <button disabled={activeSection === 0} onClick={() => setActiveSection(prev => prev - 1)} className="flex items-center gap-2 text-slate-400 font-black text-sm disabled:opacity-20 uppercase hover:text-slate-600 transition-colors">
                                                <Icon name="ArrowLeft" className="w-4 h-4" /> Zurück
                                            </button>
                                            <button disabled={activeSection === SECTIONS.length - 1} onClick={() => setActiveSection(prev => prev + 1)} className="flex items-center gap-2 text-blue-600 font-black text-sm disabled:opacity-20 uppercase hover:text-blue-800 transition-colors">
                                                Weiter <Icon name="ArrowRight" className="w-4 h-4" />
                                            </button>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        )}

                        {activeTab === 'report' && (
                            <div className="max-w-4xl mx-auto space-y-10 animate-in fade-in duration-700">
                                <div className="bg-white p-12 rounded-[2.5rem] shadow-xl text-center border border-slate-100">
                                    <h2 className="text-4xl font-black mb-12">Ergebnisbericht</h2>
                                    <div className="flex flex-col md:flex-row items-center justify-center gap-16 mb-16">
                                        <RadarChart stats={auditResults.sectionStats} />
                                        <div className="text-left space-y-4">
                                            <div>
                                                <div className="text-6xl font-black text-blue-600 leading-none">{auditResults.score} <span className="text-2xl text-slate-300 font-medium">/ {auditResults.max}</span></div>
                                                <div className="text-sm font-bold text-slate-400 uppercase tracking-widest mt-2">Gesamtpunktzahl</div>
                                            </div>
                                            <div className={`inline-block px-4 py-2 rounded-full text-[10px] font-black uppercase tracking-widest ${auditResults.percentage >= 80 ? 'bg-green-100 text-green-700' : auditResults.percentage >= 50 ? 'bg-orange-100 text-orange-700' : 'bg-red-100 text-red-700'}`}>
                                                {auditResults.percentage >= 80 ? 'Sehr gutes Schutzniveau' : auditResults.percentage >= 50 ? 'Mittlerer Handlungsbedarf' : 'Kritisches Risiko'}
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                
                                <div className="space-y-6">
                                    <div className="flex items-center gap-3">
                                        <Icon name="ClipboardCheck" className="w-6 h-6 text-blue-600" />
                                        <h3 className="text-2xl font-black text-slate-900 tracking-tight">Handlungsempfehlungen</h3>
                                    </div>
                                    <div className="grid grid-cols-1 gap-4">
                                        {SECTIONS.flatMap(s => s.questions).filter(q => responses[q.id] === 'N').map(q => (
                                            <div key={q.id} className="bg-white p-6 rounded-2xl border-l-4 border-red-500 shadow-sm flex items-start gap-4">
                                                <div className="p-2 bg-red-50 rounded-lg"><Icon name="AlertTriangle" className="w-5 h-5 text-red-500" /></div>
                                                <div>
                                                    <h4 className="font-bold text-slate-800 leading-snug">{q.text}</h4>
                                                    <p className="text-sm text-slate-500 mt-2 font-medium leading-relaxed">
                                                        <span className="text-red-600 font-bold uppercase text-[10px] block mb-1">Empfehlung:</span>
                                                        {q.recommendation}
                                                    </p>
                                                </div>
                                            </div>
                                        ))}
                                        {Object.values(responses).filter(v => v === 'N').length === 0 && Object.keys(responses).length > 0 && (
                                            <div className="bg-green-50 p-10 rounded-3xl text-center border border-green-100">
                                                <Icon name="CheckCircle2" className="w-12 h-12 text-green-600 mx-auto mb-4" />
                                                <h4 className="text-xl font-bold text-green-900">Alle Anforderungen erfüllt!</h4>
                                                <p className="text-green-700 mt-2">Ihr Unternehmen erfüllt alle Mindestanforderungen der DIN SPEC 27076.</p>
                                            </div>
                                        )}
                                    </div>
                                </div>
                            </div>
                        )}

                        {activeTab === 'info' && (
                            <div className="max-w-3xl mx-auto space-y-8 animate-in fade-in duration-500">
                                <div className="bg-white p-10 rounded-3xl border border-slate-200 shadow-sm">
                                    <h2 className="text-2xl font-black mb-6 tracking-tight">Grundlagen der DIN SPEC 27076</h2>
                                    <p className="text-slate-600 leading-relaxed mb-8">
                                        Diese DIN SPEC (PAS) wurde entwickelt, um Kleinstunternehmen (bis 50 Mitarbeiter) einen kosteneffizienten Weg zur IT-Sicherheit zu bieten. Es handelt sich um ein Basis-Sicherheitsniveau.
                                    </p>
                                    <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                                        <div className="bg-slate-50 p-6 rounded-2xl border border-slate-100">
                                            <div className="flex items-center gap-3 mb-3 text-blue-600">
                                                <Icon name="AlertTriangle" className="w-5 h-5" />
                                                <h4 className="font-bold">IT-Notfall</h4>
                                            </div>
                                            <p className="text-xs text-slate-500 leading-relaxed">Zustand, bei dem wichtige Geschäftsprozesse durch IT-Ausfälle vollständig unterbrochen sind.</p>
                                        </div>
                                        <div className="bg-slate-50 p-6 rounded-2xl border border-slate-100">
                                            <div className="flex items-center gap-3 mb-3 text-orange-600">
                                                <Icon name="Clock" className="w-5 h-5" />
                                                <h4 className="font-bold">Ungewöhnliches Ereignis</h4>
                                            </div>
                                            <p className="text-xs text-slate-500 leading-relaxed">Verdachtsmomente wie Phishing-Mails, langsame Systeme oder Verlust von Arbeitsgeräten.</p>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        )}
                    </main>
                </div>
            );
        };

        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<App />);
    </script>
</body>
</html>
