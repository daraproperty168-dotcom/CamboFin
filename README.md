import React, { useState, useEffect, useMemo } from 'react';
import { 
  Wallet, PieChart, TrendingUp, TrendingDown, 
  Settings, LogOut, Plus, Trash2, Edit, 
  Download, Printer, FileText, AlertTriangle,
  Target, CheckCircle, ArrowUpCircle, ArrowDownCircle,
  Menu, X
} from 'lucide-react';

export default function App() {
  const [user, setUser] = useState(() => JSON.parse(localStorage.getItem('financeUser')) || null);
  const [transactions, setTransactions] = useState(() => JSON.parse(localStorage.getItem('transactions')) || []);
  const [budgets, setBudgets] = useState(() => JSON.parse(localStorage.getItem('budgets')) || []);
  const [savings, setSavings] = useState(() => JSON.parse(localStorage.getItem('savings')) || []);
  const [debts, setDebts] = useState(() => JSON.parse(localStorage.getItem('debts')) || []);
  const [activeTab, setActiveTab] = useState('dashboard');
  const [displayCurrency, setDisplayCurrency] = useState('USD');
  const [isSidebarOpen, setIsSidebarOpen] = useState(false);

  useEffect(() => localStorage.setItem('financeUser', JSON.stringify(user)), [user]);
  useEffect(() => localStorage.setItem('transactions', JSON.stringify(transactions)), [transactions]);
  useEffect(() => localStorage.setItem('budgets', JSON.stringify(budgets)), [budgets]);
  useEffect(() => localStorage.setItem('savings', JSON.stringify(savings)), [savings]);
  useEffect(() => localStorage.setItem('debts', JSON.stringify(debts)), [debts]);

  if (!user) return <LoginScreen onLogin={setUser} />;

  const TABS = [
    { id: 'dashboard', label: 'ផ្ទាំងព័ត៌មាន (Dashboard)', icon: Wallet, emoji: '📊' },
    { id: 'transactions', label: 'ប្រតិបត្តិការ (Transactions)', icon: FileText, emoji: '💸' },
    { id: 'budgets', label: 'កញ្ចប់ថវិកា & សន្សំ (Budgets)', icon: Target, emoji: '🎯' },
    { id: 'debts', label: 'គ្រប់គ្រងបំណុល (Debts)', icon: AlertTriangle, emoji: '🤝' },
    { id: 'reports', label: 'របាយការណ៍ (Reports)', icon: PieChart, emoji: '📈' },
  ];

  return (
    <div className="flex h-screen bg-slate-50 font-sans selection:bg-indigo-100 selection:text-indigo-900">
      {/* Mobile Sidebar Overlay */}
      {isSidebarOpen && (
        <div 
          className="fixed inset-0 bg-slate-900/50 z-40 lg:hidden backdrop-blur-sm transition-opacity"
          onClick={() => setIsSidebarOpen(false)}
        />
      )}

      {/* Sidebar */}
      <aside className={`
        fixed lg:static inset-y-0 left-0 z-50 w-72 
        bg-white border-r border-slate-200 
        transform transition-transform duration-300 ease-in-out
        flex flex-col h-full shadow-2xl lg:shadow-none
        ${isSidebarOpen ? 'translate-x-0' : '-translate-x-full lg:translate-x-0'}
      `}>
        <div className="p-6 border-b border-slate-100 flex justify-between items-center bg-gradient-to-r from-indigo-50 to-white">
          <div className="flex items-center space-x-3">
            <div className="bg-gradient-to-br from-indigo-600 to-blue-500 p-2.5 rounded-xl shadow-lg shadow-indigo-200 text-white">
              <Wallet size={24} />
            </div>
            <div>
              <h1 className="text-xl font-black text-slate-800 tracking-tight">Cambo<span className="text-indigo-600">Fin</span></h1>
              <p className="text-[10px] uppercase font-bold text-slate-400 tracking-wider">ប្រព័ន្ធហិរញ្ញវត្ថុ</p>
            </div>
          </div>
          <button className="lg:hidden p-2 text-slate-500 hover:bg-slate-100 rounded-lg transition-colors" onClick={() => setIsSidebarOpen(false)}>
            <X size={20} />
          </button>
        </div>

        <nav className="flex-1 overflow-y-auto py-6 px-4 space-y-1.5 scrollbar-thin scrollbar-thumb-slate-200">
          {TABS.map(tab => (
            <button
              key={tab.id}
              onClick={() => { setActiveTab(tab.id); setIsSidebarOpen(false); }}
              className={`w-full flex items-center space-x-3 px-4 py-3.5 rounded-xl transition-all duration-200 group ${
                activeTab === tab.id 
                  ? 'bg-indigo-50 text-indigo-700 shadow-sm shadow-indigo-100/50 font-bold' 
                  : 'text-slate-600 hover:bg-slate-50 hover:text-slate-900 font-medium'
              }`}
            >
              <span className="text-xl group-hover:scale-110 transition-transform">{tab.emoji}</span>
              <span className="flex-1 text-left">{tab.label}</span>
            </button>
          ))}
        </nav>

        <div className="p-4 border-t border-slate-100 bg-slate-50/50">
          <div className="flex items-center justify-between px-2 mb-4">
             <span className="text-xs font-bold text-slate-500 uppercase tracking-wider">រូបិយប័ណ្ណបង្ហាញ</span>
             <select 
                value={displayCurrency} 
                onChange={(e) => setDisplayCurrency(e.target.value)}
                className="text-sm bg-white border border-slate-200 rounded-lg px-2 py-1 outline-none focus:ring-2 focus:ring-indigo-500/20 text-indigo-700 font-bold shadow-sm cursor-pointer"
              >
               <option value="USD">💵 USD</option>
               <option value="KHR">៛ KHR</option>
             </select>
          </div>
          <button 
            onClick={() => { if(confirm('តើអ្នកពិតជាចង់ចាកចេញមែនទេ?')) setUser(null); }}
            className="w-full flex items-center justify-center space-x-2 px-4 py-3 text-red-600 bg-red-50 hover:bg-red-100 rounded-xl transition-colors font-semibold"
          >
            <LogOut size={18} />
            <span>ចាកចេញ (Logout)</span>
          </button>
        </div>
      </aside>

      {/* Main Content */}
      <main className="flex-1 flex flex-col h-screen overflow-hidden">
        {/* Header (Mobile) */}
        <header className="lg:hidden bg-white border-b border-slate-200 px-4 py-3 flex items-center justify-between sticky top-0 z-30 shadow-sm">
          <button onClick={() => setIsSidebarOpen(true)} className="p-2 -ml-2 text-slate-600 hover:bg-slate-100 rounded-lg transition-colors">
            <Menu size={24} />
          </button>
          <div className="font-bold text-slate-800">CamboFin</div>
          <div className="w-8 h-8 bg-indigo-100 rounded-full flex items-center justify-center text-indigo-700 font-bold text-sm">
            {user.name.charAt(0)}
          </div>
        </header>

        {/* Dynamic Views Container */}
        <div className="flex-1 overflow-y-auto p-4 sm:p-6 lg:p-8 scrollbar-thin scrollbar-thumb-slate-200">
          <div className="max-w-6xl mx-auto space-y-8 animate-in fade-in slide-in-from-bottom-4 duration-500">
            {/* Greeting Header (Desktop) */}
            <div className="hidden lg:flex items-center justify-between mb-8 bg-white p-6 rounded-2xl shadow-sm border border-slate-100">
              <div>
                <h2 className="text-3xl font-extrabold text-slate-800 tracking-tight flex items-center gap-2">
                  សួស្តី, {user.name} <span className="text-4xl animate-wave origin-bottom-right inline-block">👋</span>
                </h2>
                <p className="text-slate-500 mt-1 font-medium">ស្វាគមន៍មកកាន់ប្រព័ន្ធគ្រប់គ្រងហិរញ្ញវត្ថុដ៏ឆ្លាតវៃរបស់អ្នក។ ✨</p>
              </div>
              <div className="w-14 h-14 bg-gradient-to-br from-indigo-100 to-blue-50 rounded-2xl flex items-center justify-center border border-indigo-100 shadow-sm">
                <span className="text-2xl font-black text-indigo-600">{user.name.charAt(0)}</span>
              </div>
            </div>

            {activeTab === 'dashboard' && <DashboardView transactions={transactions} displayCurrency={displayCurrency} budgets={budgets} />}
            {activeTab === 'transactions' && <TransactionsView transactions={transactions} setTransactions={setTransactions} displayCurrency={displayCurrency} />}
            {activeTab === 'budgets' && <BudgetsSavingsView transactions={transactions} budgets={budgets} setBudgets={setBudgets} savings={savings} setSavings={setSavings} displayCurrency={displayCurrency} />}
            {activeTab === 'debts' && <DebtsView debts={debts} setDebts={setDebts} displayCurrency={displayCurrency} />}
            {activeTab === 'reports' && <ReportsView transactions={transactions} debts={debts} budgets={budgets} savings={savings} displayCurrency={displayCurrency} />}
          </div>
        </div>
      </main>

      <style dangerouslySetInnerHTML={{__html: `
        @keyframes wave {
          0% { transform: rotate( 0.0deg) }
          10% { transform: rotate(14.0deg) }
          20% { transform: rotate(-8.0deg) }
          30% { transform: rotate(14.0deg) }
          40% { transform: rotate(-4.0deg) }
          50% { transform: rotate(10.0deg) }
          60% { transform: rotate( 0.0deg) }
          100% { transform: rotate( 0.0deg) }
        }
        .animate-wave { animation: wave 2.5s infinite; }
      `}} />
    </div>
  );
}

// ==========================================
// VIEWS COMPONENTS
// ==========================================

function LoginScreen({ onLogin }) {
  const [name, setName] = useState('');
  const [contact, setContact] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();
    if (name && contact) {
      onLogin({ name, contact, joined: new Date().toISOString() });
    }
  };

  return (
    <div className="min-h-screen bg-slate-50 flex items-center justify-center p-4 relative overflow-hidden">
      {/* Decorative background elements */}
      <div className="absolute top-[-10%] left-[-10%] w-[40%] h-[40%] bg-indigo-200/40 rounded-full blur-3xl" />
      <div className="absolute bottom-[-10%] right-[-10%] w-[40%] h-[40%] bg-blue-200/40 rounded-full blur-3xl" />
      
      <div className="bg-white/80 backdrop-blur-xl rounded-[2rem] shadow-2xl border border-white/50 w-full max-w-md p-8 sm:p-10 relative z-10 animate-in zoom-in-95 duration-500">
        <div className="flex justify-center mb-8">
          <div className="bg-gradient-to-br from-indigo-600 to-blue-500 p-5 rounded-2xl shadow-xl shadow-indigo-200">
            <Wallet size={48} className="text-white" strokeWidth={1.5} />
          </div>
        </div>
        <h1 className="text-3xl font-black text-center text-slate-800 mb-3 tracking-tight">Cambo<span className="text-indigo-600">Fin</span> 🚀</h1>
        <p className="text-center text-slate-500 mb-8 font-medium">ប្រព័ន្ធគ្រប់គ្រងហិរញ្ញវត្ថុផ្ទាល់ខ្លួន និងអាជីវកម្មខ្នាតតូចរបស់អ្នក</p>
        
        <form onSubmit={handleSubmit} className="space-y-5">
          <div className="space-y-1.5">
            <label className="block text-sm font-bold text-slate-700">ឈ្មោះរបស់អ្នក <span className="text-red-500">*</span></label>
            <div className="relative">
              <input 
                type="text" 
                required 
                value={name}
                onChange={(e) => setName(e.target.value)}
                className="w-full pl-4 pr-10 py-3.5 bg-slate-50/50 rounded-xl border border-slate-200 focus:bg-white focus:ring-4 focus:ring-indigo-500/10 focus:border-indigo-500 transition-all outline-none font-medium placeholder-slate-400"
                placeholder="ឧ. សុខ សាន្ត" 
              />
              <span className="absolute right-3 top-3.5 text-xl">👤</span>
            </div>
          </div>
          <div className="space-y-1.5">
            <label className="block text-sm font-bold text-slate-700">លេខទូរស័ព្ទ រឺ អ៊ីម៉ែល <span className="text-red-500">*</span></label>
            <div className="relative">
              <input 
                type="text" 
                required 
                value={contact}
                onChange={(e) => setContact(e.target.value)}
                className="w-full pl-4 pr-10 py-3.5 bg-slate-50/50 rounded-xl border border-slate-200 focus:bg-white focus:ring-4 focus:ring-indigo-500/10 focus:border-indigo-500 transition-all outline-none font-medium placeholder-slate-400"
                placeholder="012 345 678 / mail@example.com" 
              />
              <span className="absolute right-3 top-3.5 text-xl">📱</span>
            </div>
          </div>
          <button 
            type="submit" 
            className="w-full bg-gradient-to-r from-indigo-600 to-blue-600 hover:from-indigo-700 hover:to-blue-700 text-white font-bold py-4 rounded-xl shadow-lg shadow-indigo-200 hover:shadow-indigo-300 transition-all transform hover:-translate-y-0.5 mt-6 flex justify-center items-center gap-2"
          >
            <span>ចូលប្រើប្រាស់គណនី</span>
            <span className="text-xl">✨</span>
          </button>
        </form>
      </div>
    </div>
  );
}

function DashboardView({ transactions, displayCurrency, budgets }) {
  const currentMonth = new Date().getMonth();
  const currentYear = new Date().getFullYear();

  const stats = useMemo(() => {
    let income = 0, expense = 0;
    transactions.forEach(t => {
      const d = new Date(t.date);
      if (d.getMonth() === currentMonth && d.getFullYear() === currentYear) {
        let stdAmount = parseFloat(t.amount);
        if(t.currency === 'KHR') stdAmount = stdAmount / 4000;
        if (t.type === 'income') income += stdAmount;
        else expense += stdAmount;
      }
    });
    return { income, expense, balance: income - expense };
  }, [transactions, currentMonth, currentYear]);

  const overBudgetAlerts = useMemo(() => {
    const alerts = [];
    budgets.forEach(b => {
      let spent = 0;
      transactions.forEach(t => {
        const d = new Date(t.date);
        if (t.type === 'expense' && t.category === b.category && d.getMonth() === currentMonth && d.getFullYear() === currentYear) {
           let amt = parseFloat(t.amount);
           if (t.currency === 'KHR') amt /= 4000;
           spent += amt;
        }
      });
      let limit = parseFloat(b.limit);
      if (b.currency === 'KHR') limit /= 4000;
      
      if (spent > limit) {
        alerts.push({ category: b.category, spent, limit });
      }
    });
    return alerts;
  }, [budgets, transactions, currentMonth, currentYear]);


  return (
    <div className="space-y-6">
      <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
        <StatCard title="ចំណូលប្រចាំខែនេះ" amount={stats.income} type="income" currency={displayCurrency} icon={TrendingUp} emoji="💰" />
        <StatCard title="ចំណាយប្រចាំខែនេះ" amount={stats.expense} type="expense" currency={displayCurrency} icon={TrendingDown} emoji="💸" />
        <StatCard title="សមតុល្យ (Balance)" amount={stats.balance} type="balance" currency={displayCurrency} icon={Wallet} emoji="⚖️" />
      </div>

      {overBudgetAlerts.length > 0 && (
        <div className="bg-red-50/80 backdrop-blur-sm border border-red-200 p-5 rounded-2xl shadow-sm shadow-red-100 flex items-start gap-4">
          <div className="bg-red-100 p-3 rounded-full text-red-600 shrink-0">
            <AlertTriangle size={24} />
          </div>
          <div>
            <h4 className="text-red-800 font-bold mb-2 flex items-center gap-2">
              <span>ការព្រមាន៖ ការចំណាយលើសកញ្ចប់ថវិកា 🚨</span>
            </h4>
            <ul className="space-y-1.5 text-sm text-red-700/80 font-medium">
              {overBudgetAlerts.map((a, i) => (
                <li key={i} className="flex items-center gap-2">
                  <span className="w-1.5 h-1.5 rounded-full bg-red-400" />
                  អ្នកបានចំណាយលើសលើ <strong className="text-red-700 bg-red-100/50 px-2 py-0.5 rounded">{a.category}</strong>
                </li>
              ))}
            </ul>
          </div>
        </div>
      )}

      <div className="bg-white rounded-2xl shadow-sm border border-slate-100 overflow-hidden">
        <div className="p-5 sm:p-6 border-b border-slate-100 flex justify-between items-center bg-slate-50/50">
          <h3 className="text-lg font-black text-slate-800 flex items-center gap-2">
            <span>ប្រតិបត្តិការថ្មីៗ 📝</span>
          </h3>
        </div>
        <div className="overflow-x-auto">
          <table className="w-full text-left text-sm text-slate-600">
            <thead className="bg-slate-50 text-slate-500 uppercase text-[11px] font-extrabold tracking-wider border-b border-slate-100">
              <tr>
                <th className="px-6 py-4">កាលបរិច្ឆេទ</th>
                <th className="px-6 py-4">ប្រភេទ</th>
                <th className="px-6 py-4">ចំណាត់ថ្នាក់</th>
                <th className="px-6 py-4 text-right">ទឹកប្រាក់</th>
              </tr>
            </thead>
            <tbody className="divide-y divide-slate-50">
              {transactions.slice().reverse().slice(0, 5).map((t) => (
                <tr key={t.id} className="hover:bg-slate-50/80 transition-colors group">
                  <td className="px-6 py-4 whitespace-nowrap font-medium">{new Date(t.date).toLocaleDateString('km-KH')}</td>
                  <td className="px-6 py-4">
                    <span className={`inline-flex items-center gap-1.5 px-3 py-1 rounded-full text-xs font-bold ${
                      t.type === 'income' ? 'bg-emerald-50 text-emerald-700 border border-emerald-100' : 'bg-rose-50 text-rose-700 border border-rose-100'
                    }`}>
                      {t.type === 'income' ? '📈 ចំណូល' : '📉 ចំណាយ'}
                    </span>
                  </td>
                  <td className="px-6 py-4 font-semibold text-slate-700">{t.category}</td>
                  <td className={`px-6 py-4 text-right font-black whitespace-nowrap ${t.type === 'income' ? 'text-emerald-600' : 'text-rose-600'}`}>
                    {t.type === 'income' ? '+' : '-'}{t.amount} <span className="text-xs font-bold opacity-70">{t.currency}</span>
                  </td>
                </tr>
              ))}
              {transactions.length === 0 && (
                 <tr>
                   <td colSpan="4" className="text-center py-12 text-slate-400 font-medium">
                     <div className="text-4xl mb-3 opacity-50">📭</div>
                     មិនទាន់មានទិន្នន័យប្រតិបត្តិការទេ
                   </td>
                 </tr>
              )}
            </tbody>
          </table>
        </div>
      </div>
    </div>
  );
}

function StatCard({ title, amount, type, currency, icon: Icon, emoji }) {
  const styles = {
    income: 'from-emerald-50 to-teal-50 border-emerald-100 text-emerald-700 icon-bg-emerald-100 icon-text-emerald-600 shadow-emerald-100/50',
    expense: 'from-rose-50 to-pink-50 border-rose-100 text-rose-700 icon-bg-rose-100 icon-text-rose-600 shadow-rose-100/50',
    balance: 'from-indigo-50 to-blue-50 border-indigo-100 text-indigo-700 icon-bg-indigo-100 icon-text-indigo-600 shadow-indigo-100/50'
  };
  
  let finalAmt = amount;
  if(currency === 'KHR') finalAmt = amount * 4000;
  
  const displayAmt = currency === 'USD' 
    ? new Intl.NumberFormat('en-US', { style: 'currency', currency: 'USD' }).format(finalAmt)
    : new Intl.NumberFormat('km-KH', { style: 'currency', currency: 'KHR', maximumFractionDigits: 0 }).format(finalAmt);

  const style = styles[type];

  return (
    <div className={`p-6 rounded-3xl border bg-gradient-to-br shadow-sm flex items-center justify-between transition-transform hover:-translate-y-1 duration-300 ${style}`}>
      <div className="space-y-1">
        <p className="text-sm font-bold opacity-70 flex items-center gap-2">
          {title} <span className="text-lg">{emoji}</span>
        </p>
        <h3 className="text-3xl font-black tracking-tight">
          {displayAmt}
        </h3>
      </div>
      <div className={`p-4 rounded-2xl bg-white/60 shadow-sm backdrop-blur-sm`}>
        <Icon size={28} strokeWidth={2.5} className="opacity-80" />
      </div>
    </div>
  );
}

function TransactionsView({ transactions, setTransactions, displayCurrency }) {
  const [isFormOpen, setIsFormOpen] = useState(false);
  const CATEGORIES = {
    income: ['លក់ទំនិញ 🛍️', 'ប្រាក់ខែ 💼', 'សេវាកម្ម 🛠️', 'វិនិយោគ 📈', 'ផ្សេងៗ ✨'],
    expense: ['អាហារ 🍔', 'ការធ្វើដំណើរ 🚗', 'ទិញទំនិញ 🛒', 'ទឹកភ្លើង ⚡', 'ទីផ្សារ 📢', 'បុគ្គលិក 👥', 'ផ្សេងៗ 🧩']
  };
  const [formData, setFormData] = useState({ id: null, type: 'income', amount: '', currency: 'USD', category: CATEGORIES.income[0], date: new Date().toISOString().split('T')[0], note: '' });

  const handleSubmit = (e) => {
    e.preventDefault();
    if (formData.id) {
      setTransactions(transactions.map(t => t.id === formData.id ? { ...formData } : t));
    } else {
      setTransactions([...transactions, { ...formData, id: Date.now().toString() }]);
    }
    setIsFormOpen(false);
    resetForm();
  };

  const resetForm = () => setFormData({ id: null, type: 'income', amount: '', currency: 'USD', category: CATEGORIES.income[0], date: new Date().toISOString().split('T')[0], note: '' });

  const handleEdit = (t) => { setFormData(t); setIsFormOpen(true); };
  const handleDelete = (id) => { if (confirm('តើអ្នកពិតជាចង់លុបទិន្នន័យនេះមែនទេ? 🗑️')) setTransactions(transactions.filter(t => t.id !== id)); };

  return (
    <div className="space-y-6">
      <div className="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4 bg-white p-5 rounded-2xl shadow-sm border border-slate-100">
        <div>
          <h2 className="text-xl font-black text-slate-800 flex items-center gap-2">
            បញ្ជីប្រតិបត្តិការ <span className="text-2xl">🧾</span>
          </h2>
          <p className="text-sm text-slate-500 font-medium mt-1">គ្រប់គ្រងចំណូល និងចំណាយប្រចាំថ្ងៃរបស់អ្នក</p>
        </div>
        <button onClick={() => { resetForm(); setIsFormOpen(true); }} className="bg-indigo-600 hover:bg-indigo-700 text-white px-5 py-3 rounded-xl font-bold flex items-center space-x-2 shadow-sm shadow-indigo-200 transition-all w-full sm:w-auto justify-center group">
          <Plus size={20} className="group-hover:rotate-90 transition-transform" /> 
          <span>បន្ថែមថ្មី</span>
        </button>
      </div>

      {isFormOpen && (
        <div className="bg-white p-6 sm:p-8 rounded-3xl shadow-lg border border-slate-100 animate-in zoom-in-95 duration-200 relative overflow-hidden">
          <div className="absolute top-0 left-0 w-full h-2 bg-gradient-to-r from-indigo-500 to-blue-500" />
          <h3 className="text-xl font-black text-slate-800 mb-6 flex items-center gap-2">
            {formData.id ? 'កែប្រែប្រតិបត្តិការ ✏️' : 'បន្ថែមប្រតិបត្តិការថ្មី 📝'}
          </h3>
          <form onSubmit={handleSubmit} className="grid grid-cols-1 md:grid-cols-2 gap-5">
            <div className="space-y-1.5">
              <label className="block text-sm font-bold text-slate-700">ប្រភេទ</label>
              <select value={formData.type} onChange={(e) => setFormData({...formData, type: e.target.value, category: CATEGORIES[e.target.value][0]})} className="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl outline-none focus:ring-4 focus:ring-indigo-500/10 focus:border-indigo-500 font-bold transition-all cursor-pointer">
                <option value="income">ចំណូល 📈 (+)</option>
                <option value="expense">ចំណាយ 📉 (-)</option>
              </select>
            </div>
            <div className="flex gap-3">
               <div className="flex-1 space-y-1.5">
                <label className="block text-sm font-bold text-slate-700">ចំនួនទឹកប្រាក់</label>
                <input type="number" required min="0" step="0.01" value={formData.amount} onChange={(e) => setFormData({...formData, amount: e.target.value})} className="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl outline-none focus:ring-4 focus:ring-indigo-500/10 focus:border-indigo-500 font-bold text-lg transition-all" placeholder="0.00" />
               </div>
               <div className="w-28 space-y-1.5">
                <label className="block text-sm font-bold text-slate-700">រូបិយប័ណ្ណ</label>
                <select value={formData.currency} onChange={(e) => setFormData({...formData, currency: e.target.value})} className="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl outline-none focus:ring-4 focus:ring-indigo-500/10 focus:border-indigo-500 font-bold transition-all cursor-pointer">
                  <option value="USD">USD 💵</option>
                  <option value="KHR">KHR ៛</option>
                </select>
               </div>
            </div>
            <div className="space-y-1.5">
              <label className="block text-sm font-bold text-slate-700">ចំណាត់ថ្នាក់</label>
              <select value={formData.category} onChange={(e) => setFormData({...formData, category: e.target.value})} className="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl outline-none focus:ring-4 focus:ring-indigo-500/10 focus:border-indigo-500 font-semibold transition-all cursor-pointer">
                {CATEGORIES[formData.type].map(c => <option key={c} value={c}>{c}</option>)}
              </select>
            </div>
            <div className="space-y-1.5">
              <label className="block text-sm font-bold text-slate-700">កាលបរិច្ឆេទ</label>
              <input type="date" required value={formData.date} onChange={(e) => setFormData({...formData, date: e.target.value})} className="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl outline-none focus:ring-4 focus:ring-indigo-500/10 focus:border-indigo-500 font-medium transition-all" />
            </div>
            <div className="md:col-span-2 space-y-1.5">
              <label className="block text-sm font-bold text-slate-700">ចំណាំ (Note)</label>
              <input type="text" value={formData.note} onChange={(e) => setFormData({...formData, note: e.target.value})} className="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl outline-none focus:ring-4 focus:ring-indigo-500/10 focus:border-indigo-500 font-medium transition-all placeholder-slate-400" placeholder="ពិពណ៌នាបន្ថែមខ្លីៗ..." />
            </div>
            <div className="md:col-span-2 flex justify-end space-x-3 mt-4 pt-6 border-t border-slate-100">
              <button type="button" onClick={() => setIsFormOpen(false)} className="px-6 py-3 rounded-xl font-bold text-slate-600 bg-slate-100 hover:bg-slate-200 transition-colors">បោះបង់ ✖️</button>
              <button type="submit" className="px-8 py-3 rounded-xl font-bold text-white bg-gradient-to-r from-indigo-600 to-blue-600 hover:from-indigo-700 hover:to-blue-700 shadow-md hover:shadow-lg transition-all transform hover:-translate-y-0.5">រក្សាទុក 💾</button>
            </div>
          </form>
        </div>
      )}

      <div className="bg-white rounded-2xl shadow-sm border border-slate-100 overflow-hidden">
        <div className="overflow-x-auto">
          <table className="w-full text-left text-sm text-slate-600">
            <thead className="bg-slate-50/80 text-slate-500 uppercase text-[11px] font-extrabold tracking-wider border-b border-slate-100">
              <tr>
                <th className="px-6 py-4">កាលបរិច្ឆេទ</th>
                <th className="px-6 py-4">ប្រភេទ & ចំណាត់ថ្នាក់</th>
                <th className="px-6 py-4">ចំណាំ</th>
                <th className="px-6 py-4 text-right">ទឹកប្រាក់</th>
                <th className="px-6 py-4 text-center">សកម្មភាព</th>
              </tr>
            </thead>
            <tbody className="divide-y divide-slate-100">
              {transactions.slice().reverse().map(t => (
                <tr key={t.id} className="hover:bg-slate-50/80 transition-colors">
                  <td className="px-6 py-4 whitespace-nowrap font-medium">{new Date(t.date).toLocaleDateString('km-KH')}</td>
                  <td className="px-6 py-4">
                    <div className="flex items-center gap-2">
                      <span className={`inline-flex items-center justify-center w-8 h-8 rounded-full ${t.type === 'income' ? 'bg-emerald-100 text-emerald-700' : 'bg-rose-100 text-rose-700'}`}>
                        {t.type === 'income' ? '📈' : '📉'}
                      </span>
                      <span className="font-bold text-slate-700">{t.category}</span>
                    </div>
                  </td>
                  <td className="px-6 py-4 text-slate-500 max-w-[200px] truncate font-medium">{t.note || '-'}</td>
                  <td className={`px-6 py-4 text-right font-black whitespace-nowrap ${t.type === 'income' ? 'text-emerald-600' : 'text-rose-600'}`}>
                     {t.type === 'income' ? '+' : '-'}{t.amount} <span className="text-xs opacity-70 ml-0.5">{t.currency}</span>
                  </td>
                  <td className="px-6 py-4">
                    <div className="flex items-center justify-center gap-2">
                      <button onClick={() => handleEdit(t)} className="p-2 text-blue-600 hover:bg-blue-50 rounded-lg transition-colors" title="កែប្រែ"><Edit size={18} /></button>
                      <button onClick={() => handleDelete(t.id)} className="p-2 text-rose-600 hover:bg-rose-50 rounded-lg transition-colors" title="លុប"><Trash2 size={18} /></button>
                    </div>
                  </td>
                </tr>
              ))}
              {transactions.length === 0 && (
                <tr>
                  <td colSpan="5" className="text-center py-16 text-slate-400 font-medium">
                    <div className="text-5xl mb-4 opacity-50">📂</div>
                    មិនមានទិន្នន័យនៅឡើយទេ
                  </td>
                </tr>
              )}
            </tbody>
          </table>
        </div>
      </div>
    </div>
  );
}

function BudgetsSavingsView({ transactions, budgets, setBudgets, savings, setSavings, displayCurrency }) {
  const currentMonth = new Date().getMonth();
  const currentYear = new Date().getFullYear();
  const CATEGORIES = { expense: ['អាហារ 🍔', 'ការធ្វើដំណើរ 🚗', 'ទិញទំនិញ 🛒', 'ទឹកភ្លើង ⚡', 'ទីផ្សារ 📢', 'បុគ្គលិក 👥', 'ផ្សេងៗ 🧩'] };

  const [budgetForm, setBudgetForm] = useState({ category: CATEGORIES.expense[0], limit: '', currency: 'USD' });
  const handleAddBudget = (e) => {
    e.preventDefault();
    if(budgetForm.limit) {
      setBudgets([...budgets.filter(b => b.category !== budgetForm.category), { ...budgetForm, id: Date.now().toString() }]);
      setBudgetForm({...budgetForm, limit: ''});
    }
  };

  const getCategorySpending = (category) => {
    let spent = 0;
    transactions.forEach(t => {
      const d = new Date(t.date);
      if (t.type === 'expense' && t.category === category && d.getMonth() === currentMonth && d.getFullYear() === currentYear) {
         let amt = parseFloat(t.amount);
         if (t.currency === 'KHR') amt /= 4000;
         spent += amt;
      }
    });
    return spent;
  };

  const [savingForm, setSavingForm] = useState({ goalName: '', targetAmount: '', currency: 'USD', currentAmount: 0 });
  const handleAddSaving = (e) => {
    e.preventDefault();
    if(savingForm.goalName && savingForm.targetAmount) {
      setSavings([...savings, { ...savingForm, id: Date.now().toString() }]);
      setSavingForm({ goalName: '', targetAmount: '', currency: 'USD', currentAmount: 0 });
    }
  };
  const updateSavingAmount = (id, newAmt) => {
    setSavings(savings.map(s => s.id === id ? { ...s, currentAmount: newAmt } : s));
  };

  return (
    <div className="grid grid-cols-1 lg:grid-cols-2 gap-8">
      {/* Budgets Section */}
      <div className="space-y-6">
        <div className="bg-white p-6 rounded-3xl shadow-sm border border-slate-100">
          <h2 className="text-xl font-black text-slate-800 flex items-center mb-6">
            <div className="bg-indigo-100 p-2 rounded-xl mr-3 text-indigo-600"><PieChart size={24}/></div>
            កញ្ចប់ថវិកាប្រចាំខែ 📊
          </h2>
          <form onSubmit={handleAddBudget} className="flex gap-3 items-end flex-wrap bg-slate-50 p-4 rounded-2xl mb-6 border border-slate-100">
            <div className="flex-1 min-w-[150px] space-y-1.5">
              <label className="block text-xs font-bold text-slate-600 uppercase tracking-wide">ចំណាត់ថ្នាក់ចំណាយ</label>
              <select value={budgetForm.category} onChange={(e) => setBudgetForm({...budgetForm, category: e.target.value})} className="w-full p-2.5 bg-white border border-slate-200 rounded-xl outline-none focus:border-indigo-500 font-semibold text-sm">
                {CATEGORIES.expense.map(c => <option key={c} value={c}>{c}</option>)}
              </select>
            </div>
            <div className="flex-1 min-w-[140px] space-y-1.5">
              <label className="block text-xs font-bold text-slate-600 uppercase tracking-wide">កំណត់ទឹកប្រាក់</label>
              <div className="flex shadow-sm rounded-xl overflow-hidden border border-slate-200 bg-white focus-within:border-indigo-500 transition-colors">
                <input type="number" required value={budgetForm.limit} onChange={(e) => setBudgetForm({...budgetForm, limit: e.target.value})} className="w-full p-2.5 outline-none font-bold text-sm bg-transparent" placeholder="ឧ: 200" />
                <select value={budgetForm.currency} onChange={(e) => setBudgetForm({...budgetForm, currency: e.target.value})} className="p-2.5 bg-slate-100 border-l border-slate-200 font-black text-sm outline-none text-slate-700 cursor-pointer">
                   <option value="USD">$</option><option value="KHR">៛</option>
                </select>
              </div>
            </div>
            <button type="submit" className="bg-indigo-600 text-white p-3 rounded-xl hover:bg-indigo-700 transition-colors shadow-md shadow-indigo-200"><Plus size={20}/></button>
          </form>

          <div className="space-y-4">
            {budgets.length === 0 && <p className="text-center text-slate-400 font-medium py-6 bg-slate-50 rounded-2xl border border-dashed border-slate-200">មិនទាន់មានការកំណត់កញ្ចប់ថវិកាទេ 📝</p>}
            {budgets.map(b => {
              const spentUSD = getCategorySpending(b.category);
              let limitUSD = parseFloat(b.limit);
              if (b.currency === 'KHR') limitUSD /= 4000;
              
              const percent = Math.min((spentUSD / limitUSD) * 100, 100).toFixed(0);
              const isOver = spentUSD > limitUSD;

              return (
                <div key={b.id} className="bg-white p-5 rounded-2xl border border-slate-100 shadow-sm hover:border-slate-200 transition-colors relative overflow-hidden group">
                  {isOver && <div className="absolute top-0 left-0 w-1 h-full bg-red-500" />}
                  <div className="flex justify-between items-center mb-3">
                    <span className="font-bold text-slate-800 text-base">{b.category}</span>
                    <span className="text-sm font-black text-slate-700 bg-slate-50 px-3 py-1 rounded-lg">
                      <span className={isOver ? 'text-red-600' : 'text-indigo-600'}>{spentUSD.toFixed(2)}$</span> / <span className="opacity-70">{b.limit} {b.currency}</span>
                    </span>
                  </div>
                  <div className="w-full bg-slate-100 rounded-full h-3 overflow-hidden shadow-inner">
                    <div className={`h-full rounded-full transition-all duration-1000 ${isOver ? 'bg-gradient-to-r from-red-500 to-rose-500' : percent > 80 ? 'bg-gradient-to-r from-amber-400 to-orange-400' : 'bg-gradient-to-r from-emerald-400 to-teal-500'}`} style={{ width: `${percent}%` }}></div>
                  </div>
                  <div className="flex justify-between items-center mt-2">
                    <span className="text-[11px] font-bold text-slate-400">{percent}% បានប្រើប្រាស់</span>
                    <button onClick={() => setBudgets(budgets.filter(x => x.id !== b.id))} className="text-slate-300 hover:text-red-500 transition-colors opacity-0 group-hover:opacity-100"><Trash2 size={14}/></button>
                  </div>
                  {isOver && <p className="text-xs text-red-500 mt-3 font-bold flex items-center bg-red-50 p-2 rounded-lg"><AlertTriangle size={14} className="mr-1.5"/> លើសកម្រិតកំណត់កញ្ចប់ថវិកា</p>}
                </div>
              );
            })}
          </div>
        </div>
      </div>

      {/* Savings Section */}
      <div className="space-y-6">
        <div className="bg-white p-6 rounded-3xl shadow-sm border border-slate-100">
          <h2 className="text-xl font-black text-slate-800 flex items-center mb-6">
            <div className="bg-emerald-100 p-2 rounded-xl mr-3 text-emerald-600"><Target size={24}/></div>
            គោលដៅសន្សំប្រាក់ 🎯
          </h2>
          <form onSubmit={handleAddSaving} className="bg-slate-50 p-5 rounded-2xl border border-slate-100 grid grid-cols-2 gap-4 mb-6">
            <div className="col-span-2 space-y-1.5">
              <label className="block text-xs font-bold text-slate-600 uppercase tracking-wide">ឈ្មោះគោលដៅ 🏆</label>
              <input type="text" required value={savingForm.goalName} onChange={(e) => setSavingForm({...savingForm, goalName: e.target.value})} className="w-full p-2.5 bg-white border border-slate-200 rounded-xl outline-none focus:border-emerald-500 font-semibold" placeholder="ឧ: ទិញកុំព្យូទ័រថ្មី 💻" />
            </div>
            <div className="space-y-1.5">
              <label className="block text-xs font-bold text-slate-600 uppercase tracking-wide">ចំនួនត្រូវសន្សំ</label>
              <input type="number" required value={savingForm.targetAmount} onChange={(e) => setSavingForm({...savingForm, targetAmount: e.target.value})} className="w-full p-2.5 bg-white border border-slate-200 rounded-xl outline-none focus:border-emerald-500 font-bold" placeholder="0.00" />
            </div>
            <div className="space-y-1.5">
               <label className="block text-xs font-bold text-slate-600 uppercase tracking-wide">រូបិយប័ណ្ណ</label>
               <select value={savingForm.currency} onChange={(e) => setSavingForm({...savingForm, currency: e.target.value})} className="w-full p-2.5 bg-white border border-slate-200 rounded-xl outline-none focus:border-emerald-500 font-black cursor-pointer">
                 <option value="USD">USD 💵</option><option value="KHR">KHR ៛</option>
               </select>
            </div>
            <button type="submit" className="col-span-2 bg-emerald-600 text-white p-3 rounded-xl font-bold hover:bg-emerald-700 transition-colors shadow-md shadow-emerald-200 mt-2 flex justify-center items-center gap-2">
              <span>បង្កើតគោលដៅថ្មី</span> <span className="text-lg">✨</span>
            </button>
          </form>

          <div className="space-y-4">
            {savings.length === 0 && <p className="text-center text-slate-400 font-medium py-6 bg-slate-50 rounded-2xl border border-dashed border-slate-200">មិនទាន់មានគោលដៅសន្សំទេ 🌱</p>}
            {savings.map(s => {
              const percentNum = (s.currentAmount / s.targetAmount) * 100;
              const percent = Math.min(percentNum, 100).toFixed(0);
              const isDone = percentNum >= 100;

              return (
                <div key={s.id} className="bg-white p-5 rounded-2xl shadow-sm border border-slate-100 relative overflow-hidden group hover:border-emerald-200 transition-colors">
                  {isDone && <div className="absolute top-0 right-0 bg-emerald-500 text-white text-[10px] font-black px-3 py-1 rounded-bl-xl uppercase tracking-wider">សម្រេចគោលដៅ 🎉</div>}
                  <div className="flex justify-between items-start mb-2">
                    <div>
                      <h4 className="font-black text-slate-800 text-lg flex items-center gap-2">
                        {s.goalName} {isDone && <CheckCircle size={18} className="text-emerald-500"/>}
                      </h4>
                      <p className="text-xs font-bold text-slate-500 mt-0.5">គោលដៅ: {s.targetAmount} {s.currency}</p>
                    </div>
                    <button onClick={() => { if(confirm('លុបគោលដៅនេះ?')) setSavings(savings.filter(x => x.id !== s.id)) }} className="text-slate-300 hover:text-red-500 p-1 opacity-0 group-hover:opacity-100 transition-opacity"><Trash2 size={16}/></button>
                  </div>
                  
                  <div className="mt-4 mb-3 relative">
                    <div className="flex justify-between text-xs font-bold mb-1">
                      <span className="text-emerald-700">{s.currentAmount} {s.currency}</span>
                      <span className="text-slate-400">{percent}%</span>
                    </div>
                    <div className="flex-1 bg-slate-100 rounded-full h-3.5 overflow-hidden shadow-inner">
                      <div className={`h-full rounded-full transition-all duration-1000 ${isDone ? 'bg-gradient-to-r from-emerald-400 to-emerald-600' : 'bg-gradient-to-r from-blue-400 to-indigo-500'}`} style={{ width: `${percent}%` }}>
                        <div className="w-full h-full bg-white/20 animate-pulse"></div>
                      </div>
                    </div>
                  </div>

                  <div className="mt-4 pt-4 border-t border-slate-100">
                    <button 
                      onClick={() => {
                        const add = prompt(`បញ្ចូលទឹកប្រាក់បន្ថែមសម្រាប់ [${s.goalName}] (ជា ${s.currency}):`);
                        if(add && !isNaN(add) && Number(add) > 0) updateSavingAmount(s.id, parseFloat(s.currentAmount) + parseFloat(add));
                      }}
                      disabled={isDone}
                      className={`w-full py-2.5 rounded-xl font-bold flex items-center justify-center gap-2 transition-all ${isDone ? 'bg-slate-50 text-slate-400 cursor-not-allowed' : 'bg-emerald-50 text-emerald-700 hover:bg-emerald-100 border border-emerald-100'}`}
                    >
                      {isDone ? 'គោលដៅបានសម្រេច 🏆' : <><span>+ បន្ថែមប្រាក់សន្សំ</span> <span>💰</span></>}
                    </button>
                  </div>
                </div>
              );
            })}
          </div>
        </div>
      </div>
    </div>
  );
}

function DebtsView({ debts, setDebts, displayCurrency }) {
  const [form, setForm] = useState({ type: 'owe', personName: '', amount: '', currency: 'USD', status: 'pending' });

  const handleSubmit = (e) => {
    e.preventDefault();
    if(form.personName && form.amount) {
      setDebts([...debts, { ...form, id: Date.now().toString(), date: new Date().toISOString() }]);
      setForm({ type: 'owe', personName: '', amount: '', currency: 'USD', status: 'pending' });
    }
  };

  const toggleStatus = (id) => {
    setDebts(debts.map(d => d.id === id ? { ...d, status: d.status === 'pending' ? 'paid' : 'pending' } : d));
  };

  return (
    <div className="space-y-6">
      <div className="bg-white p-6 rounded-3xl shadow-sm border border-slate-100">
        <div className="mb-6">
          <h2 className="text-2xl font-black text-slate-800 flex items-center gap-2">
            គ្រប់គ្រងបំណុល 🤝
          </h2>
          <p className="text-sm text-slate-500 font-medium mt-1">កត់ត្រាការជំពាក់ប្រាក់ និងការឲ្យខ្ចីប្រាក់</p>
        </div>
        
        <form onSubmit={handleSubmit} className="bg-slate-50 p-5 rounded-2xl border border-slate-200 flex gap-4 items-end flex-wrap mb-8">
          <div className="space-y-1.5">
            <label className="block text-xs font-bold text-slate-600 uppercase tracking-wide">ប្រភេទកំណត់ត្រា</label>
            <select value={form.type} onChange={e => setForm({...form, type: e.target.value})} className="p-3 bg-white border border-slate-200 rounded-xl outline-none focus:border-indigo-500 font-bold cursor-pointer">
              <option value="owe">យើងជំពាក់គេ 🔻</option>
              <option value="lent">គេជំពាក់យើង 🔺</option>
            </select>
          </div>
          <div className="flex-1 min-w-[200px] space-y-1.5">
            <label className="block text-xs font-bold text-slate-600 uppercase tracking-wide">ឈ្មោះបុគ្គល / ស្ថាប័ន 👤</label>
            <input type="text" required value={form.personName} onChange={e => setForm({...form, personName: e.target.value})} className="w-full p-3 bg-white border border-slate-200 rounded-xl outline-none focus:border-indigo-500 font-semibold placeholder-slate-400" placeholder="ឈ្មោះអ្នកពាក់ព័ន្ធ..." />
          </div>
          <div className="w-36 space-y-1.5">
            <label className="block text-xs font-bold text-slate-600 uppercase tracking-wide">ទឹកប្រាក់ 💰</label>
            <input type="number" required value={form.amount} onChange={e => setForm({...form, amount: e.target.value})} className="w-full p-3 bg-white border border-slate-200 rounded-xl outline-none focus:border-indigo-500 font-black text-lg" placeholder="0.00" />
          </div>
          <div className="space-y-1.5">
            <label className="block text-xs font-bold text-slate-600 uppercase tracking-wide">រូបិយប័ណ្ណ</label>
            <select value={form.currency} onChange={e => setForm({...form, currency: e.target.value})} className="p-3 bg-white border border-slate-200 rounded-xl outline-none focus:border-indigo-500 font-black cursor-pointer">
              <option value="USD">USD 💵</option><option value="KHR">KHR ៛</option>
            </select>
          </div>
          <button type="submit" className="bg-indigo-600 text-white px-6 py-3 rounded-xl font-bold hover:bg-indigo-700 transition-colors shadow-md shadow-indigo-200 mt-2 sm:mt-0 w-full sm:w-auto h-[48px]">
            បន្ថែមបញ្ជី +
          </button>
        </form>

        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-5">
          {debts.map(d => (
            <div key={d.id} className={`p-5 rounded-2xl border transition-all duration-300 relative overflow-hidden group ${
              d.status === 'paid' 
                ? 'bg-slate-50 border-slate-200 opacity-80 scale-[0.98]' 
                : d.type === 'owe' ? 'bg-white border-rose-100 shadow-sm shadow-rose-50 hover:shadow-md' : 'bg-white border-blue-100 shadow-sm shadow-blue-50 hover:shadow-md'
            }`}>
              {d.status === 'paid' && (
                <div className="absolute inset-0 bg-slate-900/5 backdrop-blur-[1px] z-10 flex items-center justify-center pointer-events-none">
                  <div className="transform -rotate-12 border-4 border-emerald-500/30 text-emerald-600/40 text-2xl font-black px-4 py-1 rounded-xl uppercase tracking-widest">ទូទាត់រួច</div>
                </div>
              )}
              
              <div className="flex justify-between items-start mb-4">
                <div>
                  <span className={`inline-flex items-center gap-1.5 text-xs font-black px-3 py-1 rounded-full mb-3 ${
                    d.type === 'owe' ? 'bg-rose-100 text-rose-700' : 'bg-blue-100 text-blue-700'
                  }`}>
                    {d.type === 'owe' ? '🔻 យើងជំពាក់គេ' : '🔺 គេជំពាក់យើង'}
                  </span>
                  <h4 className="font-black text-slate-800 text-lg truncate pr-2" title={d.personName}>{d.personName}</h4>
                </div>
                <div className="text-right">
                  <span className={`block font-black text-2xl tracking-tight ${d.status === 'paid' ? 'text-slate-400 line-through' : d.type==='owe'?'text-rose-600':'text-blue-600'}`}>
                    {d.amount}
                  </span>
                  <span className="text-xs font-bold text-slate-500">{d.currency}</span>
                </div>
              </div>

              <div className="flex justify-between items-center mt-6 pt-4 border-t border-slate-100">
                <span className="text-[11px] font-bold text-slate-400 bg-slate-100 px-2 py-1 rounded-md">
                  📅 {new Date(d.date).toLocaleDateString('km-KH')}
                </span>
                <div className="flex gap-2 relative z-20">
                   <button 
                    onClick={() => toggleStatus(d.id)} 
                    className={`flex items-center gap-1.5 text-xs px-3 py-1.5 rounded-lg font-bold transition-colors ${
                      d.status === 'paid' 
                        ? 'bg-slate-200 text-slate-600 hover:bg-slate-300' 
                        : 'bg-emerald-100 text-emerald-700 hover:bg-emerald-200'
                    }`}
                  >
                    <CheckCircle size={14} /> {d.status === 'paid' ? 'មិនទាន់ទូទាត់?' : 'គូសថាទូទាត់រួច'}
                   </button>
                   <button onClick={() => { if(confirm('លុបកំណត់ត្រានេះ?')) setDebts(debts.filter(x => x.id !== d.id)) }} className="text-rose-400 bg-rose-50 hover:bg-rose-100 hover:text-rose-600 p-1.5 rounded-lg transition-colors"><Trash2 size={16}/></button>
                </div>
              </div>
            </div>
          ))}
          {debts.length === 0 && (
            <div className="col-span-full text-center py-12 bg-slate-50 rounded-2xl border border-dashed border-slate-200">
              <div className="text-5xl mb-3 opacity-50">✨</div>
              <p className="text-slate-500 font-bold">អ្នកមិនមានកំណត់ត្រាបំណុលទេ ឡូយណាស់! 😎</p>
            </div>
          )}
        </div>
      </div>
    </div>
  );
}

function ReportsView({ transactions, displayCurrency, debts, savings }) {
  const [filter, setFilter] = useState('month');

  const filteredTransactions = useMemo(() => {
    const now = new Date();
    return transactions.filter(t => {
      const d = new Date(t.date);
      if (filter === 'month') return d.getMonth() === now.getMonth() && d.getFullYear() === now.getFullYear();
      if (filter === 'year') return d.getFullYear() === now.getFullYear();
      if (filter === 'week') {
        const diff = now.getTime() - d.getTime();
        return diff <= 7 * 24 * 60 * 60 * 1000;
      }
      return true;
    });
  }, [transactions, filter]);

  const summary = useMemo(() => {
    let inc = 0, exp = 0;
    filteredTransactions.forEach(t => {
      let amt = parseFloat(t.amount);
      if (t.currency === 'KHR') amt /= 4000;
      if (t.type === 'income') inc += amt; else exp += amt;
    });
    return { inc, exp, bal: inc - exp };
  }, [filteredTransactions]);

  const exportCSV = () => {
    let csvContent = "data:text/csv;charset=utf-8,\uFEFF"; 
    csvContent += "កាលបរិច្ឆេទ,ប្រភេទ,ចំណាត់ថ្នាក់,ទឹកប្រាក់,រូបិយប័ណ្ណ,ចំណាំ\n";
    filteredTransactions.forEach(t => {
      csvContent += `${t.date},${t.type==='income'?'ចំណូល':'ចំណាយ'},${t.category},${t.amount},${t.currency},${t.note || ''}\n`;
    });
    const encodedUri = encodeURI(csvContent);
    const link = document.createElement("a");
    link.setAttribute("href", encodedUri);
    link.setAttribute("download", `របាយការណ៍_ហិរញ្ញវត្ថុ_${new Date().toISOString().split('T')[0]}.csv`);
    document.body.appendChild(link);
    link.click();
  };

  const handlePrintPDF = () => {
    window.print();
  };

  return (
    <div className="space-y-6 report-container">
      <div className="flex flex-col sm:flex-row justify-between items-center gap-4 bg-white p-5 rounded-2xl shadow-sm border border-slate-100 no-print">
        <div className="flex items-center space-x-3">
          <div className="bg-indigo-100 p-2.5 rounded-xl text-indigo-600"><FileText size={24}/></div>
          <h2 className="text-xl font-black text-slate-800">របាយការណ៍ហិរញ្ញវត្ថុ 📊</h2>
        </div>
        <div className="flex flex-wrap gap-3 w-full sm:w-auto">
          <select value={filter} onChange={e => setFilter(e.target.value)} className="p-2.5 border border-slate-200 rounded-xl bg-slate-50 outline-none text-sm font-bold text-slate-700 flex-1 sm:flex-none cursor-pointer focus:border-indigo-500">
            <option value="week">សប្តាហ៍នេះ 📅</option>
            <option value="month">ខែនេះ 🌙</option>
            <option value="year">ឆ្នាំនេះ 🗓️</option>
            <option value="all">ទាំងអស់ ♾️</option>
          </select>
          <div className="flex gap-2 w-full sm:w-auto">
            <button onClick={exportCSV} className="flex-1 sm:flex-none bg-emerald-50 text-emerald-700 hover:bg-emerald-100 border border-emerald-200 px-4 py-2.5 rounded-xl text-sm font-bold flex justify-center items-center transition-colors">
              <Download size={18} className="mr-2"/> <span className="hidden sm:inline">នាំចេញជា</span> CSV
            </button>
            <button onClick={handlePrintPDF} className="flex-1 sm:flex-none bg-indigo-600 hover:bg-indigo-700 text-white px-4 py-2.5 rounded-xl text-sm font-bold flex justify-center items-center shadow-md shadow-indigo-200 transition-all">
              <Printer size={18} className="mr-2"/> បោះពុម្ភ PDF
            </button>
          </div>
        </div>
      </div>

      <div className="bg-white p-6 sm:p-10 rounded-3xl shadow-lg border border-slate-100 print:shadow-none print:border-none print:p-0">
        <div className="text-center mb-10 border-b border-slate-100 pb-8 print:pb-4">
          <div className="inline-block bg-slate-100 p-4 rounded-full mb-4 print:hidden">
            <PieChart size={40} className="text-slate-700" />
          </div>
          <h1 className="text-3xl font-black text-slate-800 mb-2 tracking-tight">របាយការណ៍សង្ខេបប្រតិបត្តិការ</h1>
          <p className="text-slate-500 font-medium bg-slate-50 inline-block px-4 py-1 rounded-full text-sm mt-2 border border-slate-100">
            កាលបរិច្ឆេទបោះពុម្ភ៖ <strong className="text-slate-700">{new Date().toLocaleDateString('km-KH')}</strong> 🖨️
          </p>
        </div>

        <div className="grid grid-cols-1 sm:grid-cols-3 gap-6 mb-10">
          <div className="p-6 bg-gradient-to-br from-emerald-50 to-teal-50 rounded-2xl text-center border border-emerald-100 shadow-sm">
             <p className="text-emerald-800/60 text-sm font-bold uppercase tracking-wider mb-2">ចំណូលសរុប (USD)</p>
             <p className="text-3xl font-black text-emerald-600">${summary.inc.toFixed(2)}</p>
          </div>
          <div className="p-6 bg-gradient-to-br from-rose-50 to-pink-50 rounded-2xl text-center border border-rose-100 shadow-sm">
             <p className="text-rose-800/60 text-sm font-bold uppercase tracking-wider mb-2">ចំណាយសរុប (USD)</p>
             <p className="text-3xl font-black text-rose-600">${summary.exp.toFixed(2)}</p>
          </div>
          <div className="p-6 bg-gradient-to-br from-indigo-50 to-blue-50 rounded-2xl text-center border border-indigo-100 shadow-sm relative overflow-hidden">
             <div className="absolute -right-4 -bottom-4 text-6xl opacity-10">⚖️</div>
             <p className="text-indigo-800/60 text-sm font-bold uppercase tracking-wider mb-2 relative z-10">សមតុល្យ (សល់)</p>
             <p className={`text-3xl font-black relative z-10 ${summary.bal >= 0 ? 'text-indigo-600' : 'text-rose-600'}`}>
               ${summary.bal.toFixed(2)}
             </p>
          </div>
        </div>

        <h3 className="text-xl font-black text-slate-800 mb-4 flex items-center gap-2">
          បញ្ជីប្រតិបត្តិការលម្អិត 📋
        </h3>
        <div className="overflow-x-auto rounded-xl border border-slate-200">
          <table className="w-full text-left text-sm border-collapse">
            <thead>
              <tr className="bg-slate-100/80 print:bg-slate-200 text-slate-600 font-black text-xs uppercase tracking-wider">
                <th className="border-b border-slate-200 p-4">កាលបរិច្ឆេទ</th>
                <th className="border-b border-slate-200 p-4">ប្រភេទ</th>
                <th className="border-b border-slate-200 p-4">ចំណាត់ថ្នាក់</th>
                <th className="border-b border-slate-200 p-4 hidden sm:table-cell">ចំណាំ</th>
                <th className="border-b border-slate-200 p-4 text-right">ទឹកប្រាក់</th>
              </tr>
            </thead>
            <tbody className="divide-y divide-slate-100">
              {filteredTransactions.slice().reverse().map(t => (
                <tr key={t.id} className="hover:bg-slate-50 transition-colors">
                  <td className="p-4 font-medium text-slate-700 whitespace-nowrap">{new Date(t.date).toLocaleDateString('km-KH')}</td>
                  <td className="p-4">
                    <span className={`px-2.5 py-1 rounded-md text-xs font-bold ${t.type==='income'?'bg-emerald-50 text-emerald-700':'bg-rose-50 text-rose-700'}`}>
                      {t.type==='income'?'ចំណូល 📈':'ចំណាយ 📉'}
                    </span>
                  </td>
                  <td className="p-4 font-semibold text-slate-700">{t.category}</td>
                  <td className="p-4 text-slate-500 hidden sm:table-cell max-w-[200px] truncate">{t.note || '-'}</td>
                  <td className={`p-4 text-right font-black whitespace-nowrap ${t.type==='income'?'text-emerald-600':'text-rose-600'}`}>
                    {t.amount} <span className="text-xs opacity-70">{t.currency}</span>
                  </td>
                </tr>
              ))}
              {filteredTransactions.length === 0 && <tr><td colSpan="5" className="p-8 text-center text-slate-400 font-bold bg-slate-50">មិនមានប្រតិបត្តិការក្នុងអំឡុងពេលនេះទេ 📭</td></tr>}
            </tbody>
          </table>
        </div>
        
        <div className="mt-12 pt-8 border-t border-slate-100 hidden print:block text-center text-slate-500 text-sm font-medium">
           <p>បង្កើតដោយ CamboFin - ប្រព័ន្ធគ្រប់គ្រងហិរញ្ញវត្ថុឆ្លាតវៃ</p>
        </div>
      </div>
      
      <style>{`
        @media print {
          body * { visibility: hidden; }
          .report-container, .report-container * { visibility: visible; }
          .report-container { position: absolute; left: 0; top: 0; width: 100%; padding: 0 !important; }
          .no-print { display: none !important; }
          @page { margin: 1cm; size: A4 portrait; }
        }
      `}</style>
    </div>
  );
}
