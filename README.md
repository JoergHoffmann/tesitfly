import React, { useState, useMemo } from 'react';
import { 
  ShieldCheck, Users, Key, AlertTriangle, CheckCircle2, Info, 
  ArrowRight, ArrowLeft, Printer, HelpCircle, Clock, FileText,
  Save, Activity, HardDrive, RefreshCcw, Lock, Globe, ClipboardCheck
} from 'lucide-react';

// --- DATA: DIN SPEC 27076 ANFORDERUNGSKATALOG ---
const SECTIONS = [
  {
    id: 'org',
    title: "1. Organisation & Sensibilisierung",
    icon: <Users className="w-5 h-5" />,
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
    icon: <Key className="w-5 h-5" />,
    questions: [
      { id: '08', text: "Ist der Zutritt zu Räumen nur für Berechtigte möglich?", isTop: false, recommendation: "Kontrollieren und dokumentieren Sie den Zutritt zu IT-Räumen." },
      { id: '09', text: "Nutzen Beschäftigte individuelle und komplexe Passwörter?", isTop: false, recommendation: "Erzwingen Sie komplexe Passwörter für jedes Benutzerkonto." },
      { id: '10', text: "Wird überall dort, wo möglich, eine 2-Faktor-Authentifizierung (2FA) genutzt?", isTop: false, recommendation: "Aktivieren Sie 2FA bei Cloud-Diensten und kritischen Systemen." }
    ]
  },
  {
    id: 'backup',
    title: "3. Datensicherung",
    icon: <HardDrive className="w-5 h-5" />,
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
    icon: <RefreshCcw className="w-5 h-5" />,
    questions: [
      { id: '15', text: "Werden Updates für Systeme und Software zeitnah installiert?", isTop: true, recommendation: "Nutzen Sie automatische Updates, um Sicherheitslücken zu schließen." },
      { id: '16', text: "Gibt es eine verantwortliche Person für das Update-Management?", isTop: false, recommendation: "Bestimmen Sie, wer für die Aktualität der Software zuständig ist." },
      { id: '17', text: "Werden veraltete Systeme (ohne Sicherheitsupdates) ausgemustert?", isTop: false, recommendation: "Identifizieren und ersetzen Sie Hard-/Software ohne Herstellersupport." }
    ]
  },
  {
    id: 'malware',
    title: "5. Schutz vor Schadprogrammen",
    icon: <ShieldCheck className="w-5 h-5" />,
    questions: [
      { id: '18', text: "Verfügen alle Endgeräte über einen aktiven Virenschutz?", isTop: false, recommendation: "Statten Sie Server, PCs und Smartphones mit Schutzsoftware aus." },
      { id: '19', text: "Darf Software nur von IT-Verantwortlichen installiert werden?", isTop: false, recommendation: "Beschränken Sie die Installationsrechte auf berechtigte Personen." },
      { id: '20', text: "Sind Makros in Office-Programmen standardmäßig deaktiviert?", isTop: true, recommendation: "Deaktivieren Sie Makros und lassen Sie Aktivierung nur im begründeten Einzelfall zu." }
    ]
  },
  {
    id: 'network',
    title: "6. IT-Systeme & Netzwerke",
    icon: <Globe className="w-5 h-5" />,
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

// --- APP COMPONENT ---
const App = () => {
  const [activeTab, setActiveTab] = useState('audit');
  const [activeSection, setActiveSection] = useState(0);
  const [responses, setResponses] = useState({}); // { qId: 'J' | 'N' | 'IR' }

  const handleResponse = (id, value) => {
    setResponses(prev => ({ ...prev, [id]: value }));
  };

  // --- LOGIC: SCORE CALCULATION (DIN SPEC 27076) ---
  const auditResults = useMemo(() => {
    let totalScore = 0;
    let maxPossibleScore = 0;
    const sectionStats = SECTIONS.map(s => ({ title: s.title, score: 0, max: 0 }));

    SECTIONS.forEach((section, sIdx) => {
      section.questions.forEach(q => {
        const response = responses[q.id];
        const weight = q.isTop ? 3 : 1;

        if (response !== 'IR') { // Ignore if "Irrelevant"
          maxPossibleScore += q.isTop ? 3 : 1;
          sectionStats[sIdx].max += q.isTop ? 3 : 1;
          
          if (response === 'J') {
            totalScore += weight;
            sectionStats[sIdx].score += weight;
          } else if (response === 'N' && q.isTop) {
            totalScore -= 3; // TOP-Penalty
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

  // --- UI: SPIDER CHART (SVG) ---
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
      <svg width={size} height={size} className="mx-auto overflow-visible">
        {/* Background Circles */}
        {[0.2, 0.4, 0.6, 0.8, 1].map(r => (
          <circle key={r} cx={center} cy={center} r={radius * r} fill="none" stroke="#e2e8f0" strokeWidth="1" />
        ))}
        {/* Axes */}
        {axisPoints.map((p, i) => (
          <line key={i} x1={center} y1={center} x2={p.x2} y2={p.y2} stroke="#cbd5e1" strokeWidth="1" />
        ))}
        {/* Data Shape */}
        <polygon points={points} fill="rgba(37, 99, 235, 0.2)" stroke="#2563eb" strokeWidth="2" />
        {/* Labels */}
        {stats.map((s, i) => {
          const angle = (Math.PI * 2 * i) / stats.length - Math.PI / 2;
          const labelRadius = radius + 30;
          const x = center + labelRadius * Math.cos(angle);
          const y = center + labelRadius * Math.sin(angle);
          return (
            <text key={i} x={x} y={y} fontSize="10" textAnchor="middle" className="fill-slate-500 font-medium">
              {s.title.split('. ')[1].substring(0, 15)}...
            </text>
          );
        })}
      </svg>
    );
  };

  return (
    <div className="min-h-screen bg-[#F8FAFC] font-sans text-slate-900 pb-20">
      {/* Navbar */}
      <header className="bg-white border-b sticky top-0 z-50">
        <div className="max-w-6xl mx-auto px-6 h-18 flex items-center justify-between">
          <div className="flex items-center gap-3">
            <div className="bg-blue-600 p-2 rounded-lg shadow-lg shadow-blue-200">
              <ShieldCheck className="text-white w-6 h-6" />
            </div>
            <div>
              <h1 className="font-bold text-lg leading-tight">IT-Check DIN SPEC 27076</h1>
              <p className="text-[10px] text-slate-400 uppercase tracking-widest font-bold">Standard für Kleinstunternehmen</p>
            </div>
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
                {tab === 'audit' ? 'Fragebogen' : tab === 'report' ? 'Ergebnisbericht' : 'Basis-Wissen'}
              </button>
            ))}
          </nav>
          
          <button className="flex items-center gap-2 px-4 py-2 border border-slate-200 rounded-xl text-sm font-bold text-slate-600 hover:bg-slate-50 transition-colors">
            <Printer className="w-4 h-4" /> <span className="hidden sm:inline">Drucken</span>
          </button>
        </div>
      </header>

      <main className="max-w-6xl mx-auto px-6 py-10">
        {activeTab === 'audit' && (
          <div className="grid grid-cols-1 lg:grid-cols-12 gap-10">
            {/* Sidebar Navigation */}
            <div className="lg:col-span-3 space-y-4">
              <div className="bg-white p-5 rounded-2xl shadow-sm border border-slate-200">
                <h3 className="text-xs font-bold text-slate-400 uppercase tracking-wider mb-4">Bereiche</h3>
                <div className="space-y-2">
                  {SECTIONS.map((s, idx) => (
                    <button
                      key={s.id}
                      onClick={() => setActiveSection(idx)}
                      className={`w-full flex items-center gap-3 p-3 rounded-xl transition-all text-left group ${
                        activeSection === idx ? 'bg-blue-50 text-blue-700 shadow-sm ring-1 ring-blue-100' : 'text-slate-500 hover:bg-slate-50'
                      }`}
                    >
                      <div className={`p-2 rounded-lg transition-colors ${activeSection === idx ? 'bg-blue-600 text-white' : 'bg-slate-100 group-hover:bg-slate-200'}`}>
                        {s.icon}
                      </div>
                      <span className="text-sm font-bold leading-snug">{s.title.split('. ')[1]}</span>
                    </button>
                  ))}
                </div>
              </div>

              <div className="bg-gradient-to-br from-blue-600 to-indigo-700 p-6 rounded-2xl shadow-xl text-white">
                <div className="flex items-center justify-between mb-4">
                  <Activity className="w-6 h-6 opacity-50" />
                  <span className="text-2xl font-black">{auditResults.percentage}%</span>
                </div>
                <div className="text-xs font-bold opacity-80 uppercase mb-1">Risiko-Status</div>
                <div className="w-full bg-white/20 h-2 rounded-full overflow-hidden">
                  <div className="bg-white h-full transition-all duration-700" style={{ width: `${auditResults.percentage}%` }}></div>
                </div>
                <p className="mt-4 text-[11px] leading-relaxed opacity-70">
                  Punkte: {auditResults.score} von {auditResults.max}
                </p>
              </div>
            </div>

            {/* Questions Area */}
            <div className="lg:col-span-9">
              <div className="bg-white rounded-3xl shadow-sm border border-slate-200 overflow-hidden">
                <div className="p-8 border-b border-slate-100 bg-slate-50/50 flex items-center justify-between">
                  <div className="flex items-center gap-4">
                    <div className="p-3 bg-white rounded-2xl shadow-sm border border-slate-100">
                      {SECTIONS[activeSection].icon}
                    </div>
                    <div>
                      <h2 className="text-xl font-black text-slate-800">{SECTIONS[activeSection].title}</h2>
                      <p className="text-sm text-slate-400 font-medium">Beantworten Sie die folgenden Leitfragen nach bestem Gewissen.</p>
                    </div>
                  </div>
                </div>
                
                <div className="divide-y divide-slate-50">
                  {SECTIONS[activeSection].questions.map((q) => (
                    <div key={q.id} className="p-8 hover:bg-slate-50/50 transition-colors">
                      <div className="flex flex-col lg:flex-row gap-6 items-start lg:items-center">
                        <div className="flex-1 space-y-2">
                          <div className="flex items-center gap-3">
                            <span className="text-[10px] font-black bg-slate-100 text-slate-500 px-2 py-1 rounded">#{q.id}</span>
                            {q.isTop && <span className="text-[10px] font-black bg-orange-100 text-orange-600 px-2 py-1 rounded border border-orange-200 flex items-center gap-1"><AlertTriangle className="w-3 h-3" /> TOP</span>}
                          </div>
                          <p className="text-slate-800 font-bold text-lg leading-tight">{q.text}</p>
                        </div>
                        
                        <div className="flex flex-wrap gap-2 w-full lg:w-auto">
                          {[
                            { label: 'Erfüllt', val: 'J', color: 'bg-green-600' },
                            { label: 'Nicht erfüllt', val: 'N', color: 'bg-red-600' },
                            { label: 'Irrelevant', val: 'IR', color: 'bg-slate-400' }
                          ].map(btn => (
                            <button
                              key={btn.val}
                              onClick={() => handleResponse(q.id, btn.val)}
                              className={`flex-1 lg:flex-none px-5 py-2.5 rounded-xl font-black text-xs transition-all border ${
                                responses[q.id] === btn.val
                                  ? `${btn.color} text-white border-transparent shadow-lg scale-105`
                                  : 'bg-white text-slate-500 border-slate-200 hover:border-slate-300 hover:bg-slate-50'
                              }`}
                            >
                              {btn.label}
                            </button>
                          ))}
                        </div>
                      </div>
                    </div>
                  ))}
                </div>

                <div className="p-8 bg-slate-50 flex justify-between">
                  <button 
                    disabled={activeSection === 0}
                    onClick={() => setActiveSection(prev => prev - 1)}
                    className="flex items-center gap-2 text-slate-400 font-black text-sm uppercase hover:text-slate-700 disabled:opacity-20 transition-all"
                  >
                    <ArrowLeft className="w-4 h-4" /> Zurück
                  </button>
                  <button 
                    disabled={activeSection === SECTIONS.length - 1}
                    onClick={() => setActiveSection(prev => prev + 1)}
                    className="flex items-center gap-2 text-blue-600 font-black text-sm uppercase hover:text-blue-800 disabled:opacity-20 transition-all"
                  >
                    Nächster Bereich <ArrowRight className="w-4 h-4" />
                  </button>
                </div>
              </div>
            </div>
          </div>
        )}

        {activeTab === 'report' && (
          <div className="max-w-4xl mx-auto space-y-10 animate-in fade-in slide-in-from-bottom-8 duration-500">
            <div className="bg-white p-12 rounded-[2.5rem] shadow-xl border border-slate-100 text-center relative overflow-hidden">
               {/* Decorative Background */}
              <div className="absolute top-0 right-0 p-10 opacity-5">
                <ClipboardCheck className="w-40 h-40" />
              </div>

              <div className="relative z-10">
                <h2 className="text-4xl font-black text-slate-900 mb-6">Ergebnisbericht</h2>
                <p className="text-slate-500 text-lg mb-12 max-w-2xl mx-auto font-medium">
                  Basierend auf der DIN SPEC 27076 wurde für Ihr Unternehmen folgender Risiko-Status ermittelt.
                </p>

                <div className="flex flex-col md:flex-row items-center justify-center gap-16 mb-16">
                  <div className="relative">
                    <RadarChart stats={auditResults.sectionStats} />
                  </div>
                  <div className="space-y-4">
                    <div className="text-[80px] font-black text-blue-600 leading-none">
                      {auditResults.score}
                      <span className="text-2xl text-slate-300 font-medium ml-2">/ {auditResults.max}</span>
                    </div>
                    <div className="text-xl font-bold text-slate-400 uppercase tracking-widest">Punktzahl</div>
                    <div className={`inline-block px-6 py-2 rounded-full text-sm font-black uppercase tracking-widest ${
                      auditResults.percentage > 80 ? 'bg-green-100 text-green-700' : 
                      auditResults.percentage > 50 ? 'bg-orange-100 text-orange-700' : 'bg-red-100 text-red-700'
                    }`}>
                      {auditResults.percentage > 80 ? 'Guter Schutz' : auditResults.percentage > 50 ? 'Handlungsbedarf' : 'Hohes Risiko'}
                    </div>
                  </div>
                </div>
              </div>
            </div>

            <div className="space-y-6">
              <h3 className="text-2xl font-black text-slate-900 flex items-center gap-3">
                <ClipboardCheck className="w-6 h-6 text-blue-600" />
                Handlungsempfehlungen
              </h3>
              
              <div className="grid grid-cols-1 gap-4">
                {SECTIONS.flatMap(s => s.questions)
                  .filter(q => responses[q.id] === 'N')
                  .map(q => (
                    <div key={q.id} className="bg-white p-6 rounded-2xl border-l-4 border-red-500 shadow-sm flex flex-col md:flex-row gap-6 items-start md:items-center">
                      <div className="flex-1">
                        <div className="flex items-center gap-2 mb-1">
                          <span className="text-[10px] font-black bg-red-50 text-red-600 px-2 py-1 rounded">#{q.id}</span>
                          {q.isTop && <span className="text-[10px] font-black bg-orange-100 text-orange-600 px-2 py-1 rounded">TOP</span>}
                        </div>
                        <h4 className="font-bold text-slate-800">{q.text}</h4>
                        <p className="text-sm text-slate-500 mt-2 italic">Empfehlung: {q.recommendation}</p>
                      </div>
                      <div className="bg-red-50 p-4 rounded-xl shrink-0">
                         <AlertTriangle className="w-5 h-5 text-red-500" />
                      </div>
                    </div>
                  ))
                }
                {Object.values(responses).filter(v => v === 'N').length === 0 && (
                  <div className="bg-green-50 p-12 rounded-[2rem] text-center border border-green-100">
                    <CheckCircle2 className="w-16 h-16 text-green-600 mx-auto mb-6" />
                    <h4 className="text-2xl font-black text-green-900 mb-4">Hervorragend!</h4>
                    <p className="text-green-700 max-w-md mx-auto">
                      Ihr Unternehmen erfüllt alle Anforderungen nach DIN SPEC 27076. Nehmen Sie nun weiterführende Zertifizierungen in den Blick.
                    </p>
                  </div>
                )}
              </div>
            </div>
          </div>
        )}

        {activeTab === 'info' && (
          <div className="max-w-4xl mx-auto space-y-8 animate-in fade-in slide-in-from-bottom-4 duration-500">
             <div className="bg-white p-10 rounded-3xl shadow-sm border border-slate-200">
              <h2 className="text-2xl font-black mb-6">Was ist die DIN SPEC 27076?</h2>
              <p className="text-slate-600 leading-relaxed mb-6">
                Die DIN SPEC 27076 wurde speziell für kleine und Kleinstunternehmen (KKU) entwickelt. Sie bietet einen standardisierten Beratungsprozess, der zeit- und kosteneffizient ein „Mindestmaß“ an IT-Sicherheit etabliert. 
              </p>
              <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
                {[
                  { title: "Notfallplan", text: "Schritte zur Wiederherstellung nach einem IT-Vorfall.", icon: <FileText className="text-blue-600" /> },
                  { title: "Ungewöhnliches Ereignis", text: "Abweichungen wie Phishing oder unbekannte Fehler.", icon: <Clock className="text-orange-600" /> },
                  { title: "IT-Notfall", text: "Kritischer Stillstand von Geschäftsprozessen durch IT-Ausfall.", icon: <AlertTriangle className="text-red-600" /> }
                ].map((item, idx) => (
                  <div key={idx} className="bg-slate-50 p-6 rounded-2xl border border-slate-100">
                    <div className="mb-4">{item.icon}</div>
                    <h4 className="font-black text-slate-800 mb-2">{item.title}</h4>
                    <p className="text-sm text-slate-500 leading-snug">{item.text}</p>
                  </div>
                ))}
              </div>
            </div>
          </div>
        )}
      </main>

      <footer className="bg-white border-t py-12">
        <div className="max-w-6xl mx-auto px-6 flex flex-col md:flex-row justify-between items-center gap-8">
          <div className="flex items-center gap-3 opacity-50 grayscale hover:grayscale-0 transition-all">
            <ShieldCheck className="w-8 h-8 text-blue-600" />
            <div>
              <p className="text-sm font-black text-slate-800 leading-none">DIN SPEC 27076</p>
              <p className="text-[10px] text-slate-500 uppercase tracking-widest font-bold">Standard Konform</p>
            </div>
          </div>
          <div className="flex gap-8 text-xs font-black text-slate-400 uppercase tracking-widest">
            <a href="#" className="hover:text-blue-600 transition-colors">Förderdatenbank</a>
            <a href="#" className="hover:text-blue-600 transition-colors">BSI KMU-Portal</a>
            <a href="#" className="hover:text-blue-600 transition-colors">Impressum</a>
          </div>
          <p className="text-xs text-slate-400 font-medium italic">Ein Unternehmen der Informationssicherheit.</p>
        </div>
      </footer>
    </div>
  );
};

export default App;
