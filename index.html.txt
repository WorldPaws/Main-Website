import React, { useState, useEffect } from 'react';
import { 
  Heart, PawPrint, Home, Gift, Menu, X, ChevronRight, 
  Mail, Phone, MapPin, Star, CreditCard, CheckCircle2,
  ArrowRight, Filter, Info, Camera, Calendar, Copy,
  Bitcoin, Wallet, ExternalLink, Zap, Globe, Trophy,
  ShieldCheck, AlertCircle
} from 'lucide-react';

const App = () => {
  const [activePage, setActivePage] = useState('home'); 
  const [isMenuOpen, setIsMenuOpen] = useState(false);
  const [isDonationOpen, setIsDonationOpen] = useState(false);
  const [isFormOpen, setIsFormOpen] = useState(false);
  const [isCryptoMode, setIsCryptoMode] = useState(false);
  const [scrolled, setScrolled] = useState(false);
  const [selectedPet, setSelectedPet] = useState(null);
  
  // Web3 State
  const [walletAddress, setWalletAddress] = useState(null);
  const [walletType, setWalletType] = useState(null); // 'metamask' or 'phantom'
  const [isConnecting, setIsConnecting] = useState(false);

  // Logo from user upload
  const brandLogo = "image.png";

  useEffect(() => {
    const handleScroll = () => setScrolled(window.scrollY > 50);
    window.addEventListener('scroll', handleScroll);
    return () => window.removeEventListener('scroll', handleScroll);
  }, []);

  const navigate = (page) => {
    setActivePage(page);
    window.scrollTo(0, 0);
    setIsMenuOpen(false);
  };

  // --- WALLET LOGIC ---
  const connectMetaMask = async () => {
    if (typeof window.ethereum !== 'undefined') {
      try {
        setIsConnecting(true);
        const accounts = await window.ethereum.request({ method: 'eth_requestAccounts' });
        setWalletAddress(accounts[0]);
        setWalletType('metamask');
      } catch (err) {
        console.error("MetaMask connection failed", err);
      } finally {
        setIsConnecting(false);
      }
    } else {
      window.open('https://metamask.io/download/', '_blank');
    }
  };

  const connectPhantom = async () => {
    const isPhantomAvailable = window.solana && window.solana.isPhantom;
    if (isPhantomAvailable) {
      try {
        setIsConnecting(true);
        const resp = await window.solana.connect();
        setWalletAddress(resp.publicKey.toString());
        setWalletType('phantom');
      } catch (err) {
        console.error("Phantom connection failed", err);
      } finally {
        setIsConnecting(false);
      }
    } else {
      window.open('https://phantom.app/', '_blank');
    }
  };

  const disconnectWallet = () => {
    setWalletAddress(null);
    setWalletType(null);
  };

  const truncateAddress = (addr) => `${addr.slice(0, 6)}...${addr.slice(-4)}`;

  // --- COMPONENT: DONATION MODAL ---
  const DonationModal = () => (
    <div className="fixed inset-0 z-[100] flex items-center justify-center p-4 bg-slate-900/95 backdrop-blur-md">
      <div className="bg-white rounded-[40px] w-full max-w-xl p-8 relative shadow-2xl animate-in zoom-in duration-300 max-h-[90vh] overflow-y-auto">
        <button onClick={() => { setIsDonationOpen(false); setIsCryptoMode(false); }} className="absolute top-6 right-6 p-2 hover:bg-slate-100 rounded-full">
          <X className="w-6 h-6" />
        </button>

        <div className="text-center mb-6">
           <div className="w-20 h-20 mx-auto mb-4 bg-yellow-50 rounded-full flex items-center justify-center overflow-hidden border border-yellow-100 shadow-sm">
              <img src={brandLogo} alt="WPO Coin" className="w-full h-full object-contain scale-[1.7]" />
           </div>
           <h2 className="text-3xl font-black">Support the Rescue</h2>
           <p className="text-slate-500">Choose your preferred way to give.</p>
        </div>

        {!walletAddress ? (
          <div className="space-y-4">
            <button 
              onClick={connectMetaMask}
              className="w-full flex items-center justify-between p-5 border-2 border-slate-100 rounded-[24px] hover:border-yellow-500 hover:bg-yellow-50 transition-all group"
            >
              <div className="flex items-center space-x-4">
                <div className="w-12 h-12 bg-yellow-100 rounded-xl flex items-center justify-center font-bold text-yellow-600">M</div>
                <div className="text-left">
                  <p className="font-black">MetaMask</p>
                  <p className="text-xs text-slate-400 font-bold uppercase tracking-wider">Ethereum / Polygon</p>
                </div>
              </div>
              <ChevronRight className="group-hover:translate-x-1 transition-transform" />
            </button>

            <button 
              onClick={connectPhantom}
              className="w-full flex items-center justify-between p-5 border-2 border-slate-100 rounded-[24px] hover:border-indigo-500 hover:bg-indigo-50 transition-all group"
            >
              <div className="flex items-center space-x-4">
                <div className="w-12 h-12 bg-indigo-100 rounded-xl flex items-center justify-center font-bold text-indigo-600 font-serif italic text-xl">P</div>
                <div className="text-left">
                  <p className="font-black">Phantom</p>
                  <p className="text-xs text-slate-400 font-bold uppercase tracking-wider">Solana Network</p>
                </div>
              </div>
              <ChevronRight className="group-hover:translate-x-1 transition-transform" />
            </button>

            <div className="relative py-4">
              <div className="absolute inset-0 flex items-center"><div className="w-full border-t border-slate-100"></div></div>
              <div className="relative flex justify-center text-xs uppercase font-black text-slate-400 tracking-widest"><span className="bg-white px-4">Or use Traditional</span></div>
            </div>

            <button className="w-full bg-slate-900 text-white py-5 rounded-[24px] font-bold flex items-center justify-center space-x-2 hover:bg-yellow-500 transition-colors">
              <CreditCard className="w-5 h-5" />
              <span>Credit Card / Apple Pay</span>
            </button>
          </div>
        ) : (
          <div className="space-y-6 text-center py-4 animate-in fade-in">
            <div className="bg-green-50 p-6 rounded-[32px] border border-green-100">
              <CheckCircle2 className="w-12 h-12 text-green-500 mx-auto mb-3" />
              <p className="font-black text-slate-900">Wallet Connected</p>
              <p className="text-sm font-mono text-slate-500">{truncateAddress(walletAddress)}</p>
            </div>
            
            <div className="grid grid-cols-2 gap-4">
               {['0.1', '0.5', '1.0', '5.0'].map(val => (
                 <button key={val} className="p-4 bg-slate-50 rounded-2xl border border-slate-100 font-black hover:border-yellow-500 hover:text-yellow-600 transition-all">
                   {val} {walletType === 'phantom' ? 'SOL' : 'ETH'}
                 </button>
               ))}
            </div>

            <button className="w-full bg-yellow-500 text-white py-5 rounded-[24px] font-black shadow-xl shadow-yellow-200 hover:bg-yellow-600">
              Complete Donation
            </button>
            
            <button onClick={disconnectWallet} className="text-xs font-bold text-slate-400 hover:text-rose-500 uppercase tracking-widest">
              Disconnect Wallet
            </button>
          </div>
        )}
      </div>
    </div>
  );

  return (
    <div className="min-h-screen bg-white font-sans text-slate-900 selection:bg-yellow-100">
      {isDonationOpen && <DonationModal />}

      {/* Navigation */}
      <nav className={`fixed w-full z-50 transition-all duration-300 ${scrolled ? 'bg-white/95 backdrop-blur-md shadow-sm py-3' : 'bg-transparent py-5'}`}>
        <div className="max-w-7xl mx-auto px-6 flex justify-between items-center">
          <button onClick={() => navigate('home')} className="flex items-center space-x-3 group">
            <div className="w-10 h-10 bg-yellow-50 rounded-lg flex items-center justify-center overflow-hidden border border-yellow-100 group-hover:rotate-12 transition-transform">
              <img src={brandLogo} alt="Logo" className="w-full h-full object-contain scale-[1.7]" />
            </div>
            <span className="text-xl font-black tracking-tighter text-slate-800 uppercase">PawForward</span>
          </button>

          <div className="hidden md:flex items-center space-x-8 text-xs uppercase tracking-[0.2em] font-black">
            <button onClick={() => navigate('home')} className={activePage === 'home' ? 'text-yellow-600' : 'text-slate-500 hover:text-yellow-500'}>Home</button>
            <button onClick={() => navigate('adopt')} className={activePage === 'adopt' ? 'text-yellow-600' : 'text-slate-500 hover:text-yellow-500'}>Adopt</button>
            <button onClick={() => navigate('stories')} className={activePage === 'stories' ? 'text-yellow-600' : 'text-slate-500 hover:text-yellow-500'}>Impact</button>
            
            {walletAddress ? (
              <div className="flex items-center space-x-3 bg-slate-100 px-4 py-2 rounded-full border border-slate-200">
                <div className={`w-2 h-2 rounded-full animate-pulse ${walletType === 'phantom' ? 'bg-indigo-500' : 'bg-yellow-500'}`}></div>
                <span className="font-mono lowercase text-[10px] tracking-normal">{truncateAddress(walletAddress)}</span>
                <button onClick={disconnectWallet} className="text-slate-400 hover:text-rose-500"><X className="w-3 h-3" /></button>
              </div>
            ) : (
              <button 
                onClick={() => setIsDonationOpen(true)} 
                className="bg-slate-900 text-white px-8 py-3 rounded-full hover:bg-yellow-500 transition-all shadow-xl shadow-slate-200 flex items-center space-x-2"
              >
                <Wallet className="w-4 h-4" />
                <span>Connect</span>
              </button>
            )}
          </div>
        </div>
      </nav>

      {/* PAGE: HOME */}
      {activePage === 'home' && (
        <>
          {/* Hero */}
          <section className="relative pt-32 pb-20 lg:pt-56 overflow-hidden">
            <div className="max-w-7xl mx-auto px-6 relative z-10">
              <div className="max-w-3xl space-y-8">
                <div className="inline-flex items-center space-x-3 bg-yellow-100 text-yellow-800 px-6 py-2 rounded-full text-[10px] font-black uppercase tracking-widest border border-yellow-200">
                  <ShieldCheck className="w-4 h-4" />
                  <span>On-Chain Verified Transparency</span>
                </div>
                <h1 className="text-7xl lg:text-9xl font-black leading-[0.85] tracking-tighter text-slate-900">
                  Save a Pet <br/> <span className="text-yellow-500">Decentralize</span> Care.
                </h1>
                <p className="text-xl text-slate-500 leading-relaxed max-w-xl font-medium">
                  Connect your wallet and watch your contribution transform into real medical aid and food for rescued animals globally.
                </p>
                <div className="flex flex-col sm:flex-row gap-4 pt-4">
                  <button onClick={() => setIsDonationOpen(true)} className="bg-yellow-500 text-white px-10 py-6 rounded-[32px] font-black text-xl hover:bg-slate-900 transition-all shadow-2xl shadow-yellow-200">
                    Donate Now
                  </button>
                  <button onClick={() => navigate('adopt')} className="bg-white border-2 border-slate-100 text-slate-900 px-10 py-6 rounded-[32px] font-black text-xl hover:border-yellow-500 transition-all">
                    Browse Adoptions
                  </button>
                </div>
              </div>
            </div>
            
            {/* Visual Element */}
            <div className="absolute top-20 right-[-10%] w-1/2 h-[120%] bg-yellow-50 rounded-l-[200px] -z-10 hidden lg:block overflow-hidden">
              <img src="https://images.unsplash.com/photo-1596492784531-6e6eb5ea9993?auto=format&fit=crop&q=80&w=800" className="w-full h-full object-cover opacity-80 mix-blend-multiply" alt="Dog" />
            </div>
          </section>

          {/* SECTION: LIVE IMPACT MAP */}
          <section className="py-24 bg-slate-950 text-white relative overflow-hidden">
            <div className="max-w-7xl mx-auto px-6 relative z-10">
              <div className="grid lg:grid-cols-2 gap-20 items-center">
                <div className="space-y-8">
                  <div className="flex items-center space-x-3">
                    <div className="p-3 bg-yellow-500 rounded-2xl text-slate-950"><Globe className="w-6 h-6" /></div>
                    <h2 className="text-4xl lg:text-5xl font-black uppercase tracking-tighter">Live Rescue Map</h2>
                  </div>
                  <p className="text-slate-400 text-lg leading-relaxed font-medium">
                    Our network spans across 12 countries. Each dot on the map represents a real shelter supported by your crypto donations.
                  </p>
                  <div className="space-y-4">
                    {[
                      { city: 'San Francisco, USA', task: 'Emergency Surgery', status: 'In Progress', color: 'bg-green-500' },
                      { city: 'Athens, Greece', task: 'Shelter Expansion', status: 'Pending Funds', color: 'bg-yellow-500' },
                      { city: 'Bali, Indonesia', task: 'Mass Vaccination', status: 'Completed', color: 'bg-blue-500' }
                    ].map((item, idx) => (
                      <div key={idx} className="bg-slate-900 p-5 rounded-[24px] border border-slate-800 flex items-center justify-between group hover:border-yellow-500 transition-all">
                        <div className="flex items-center space-x-4">
                           <div className={`w-2 h-2 rounded-full ${item.color} animate-pulse`}></div>
                           <div>
                             <p className="font-bold">{item.city}</p>
                             <p className="text-xs text-slate-500 uppercase font-bold tracking-widest">{item.task}</p>
                           </div>
                        </div>
                        <ArrowRight className="w-5 h-5 text-slate-700 group-hover:text-yellow-500 transition-colors" />
                      </div>
                    ))}
                  </div>
                </div>
                
                {/* SVG MAP VISUAL */}
                <div className="relative aspect-square bg-slate-900/50 rounded-[60px] border border-slate-800 p-8 flex items-center justify-center">
                  <svg viewBox="0 0 800 400" className="w-full h-auto opacity-20">
                    <path fill="currentColor" d="M150 100 Q 400 50 650 100 Q 750 200 650 300 Q 400 350 150 300 Q 50 200 150 100" />
                  </svg>
                  {/* Pulse points */}
                  <div className="absolute top-[30%] left-[25%]"><div className="w-4 h-4 bg-yellow-500 rounded-full animate-ping"></div></div>
                  <div className="absolute top-[45%] left-[65%]"><div className="w-4 h-4 bg-blue-500 rounded-full animate-ping"></div></div>
                  <div className="absolute top-[70%] left-[40%]"><div className="w-4 h-4 bg-green-500 rounded-full animate-ping"></div></div>
                </div>
              </div>
            </div>
          </section>

          {/* SECTION: LEADERBOARD */}
          <section className="py-24 bg-white">
            <div className="max-w-7xl mx-auto px-6 text-center mb-16 space-y-4">
              <Trophy className="w-12 h-12 text-yellow-500 mx-auto" />
              <h2 className="text-5xl font-black uppercase tracking-tighter">Philanthropist Leaderboard</h2>
              <p className="text-slate-500 max-w-xl mx-auto font-medium">Top contributors this month helping animals find their forever homes.</p>
            </div>
            
            <div className="max-w-4xl mx-auto space-y-4">
              {[
                { rank: 1, name: "pawsome.eth", amount: "12.5 ETH", pets: 42, user_img: "https://i.pravatar.cc/100?img=32" },
                { rank: 2, name: "sol_lover.sol", amount: "840 SOL", pets: 38, user_img: "https://i.pravatar.cc/100?img=11" },
                { rank: 3, name: "crypto_cat.eth", amount: "5.2 ETH", pets: 19, user_img: "https://i.pravatar.cc/100?img=45" }
              ].map((user, idx) => (
                <div key={idx} className="flex items-center justify-between p-6 bg-slate-50 rounded-[32px] border border-slate-100 hover:bg-yellow-50 hover:border-yellow-200 transition-all group">
                  <div className="flex items-center space-x-6">
                    <span className="text-2xl font-black text-slate-300 group-hover:text-yellow-300 w-8">#{user.rank}</span>
                    <img src={user.user_img} className="w-14 h-14 rounded-2xl bg-slate-200 object-cover" alt="" />
                    <div>
                       <p className="font-black text-xl">{user.name}</p>
                       <p className="text-xs font-bold text-slate-400 uppercase tracking-widest">{user.pets} Animals Helped</p>
                    </div>
                  </div>
                  <div className="text-right">
                    <p className="text-2xl font-black text-slate-900">{user.amount}</p>
                    <p className="text-[10px] font-black text-yellow-600 uppercase tracking-widest">Top Donor</p>
                  </div>
                </div>
              ))}
            </div>
          </section>
        </>
      )}

      {/* --- PAGE: ADOPT --- */}
      {activePage === 'adopt' && (
        <section className="pt-32 pb-24 bg-slate-50 min-h-screen">
          <div className="max-w-7xl mx-auto px-6">
             <div className="flex flex-col md:flex-row items-end justify-between mb-16 gap-6">
               <h2 className="text-6xl font-black uppercase tracking-tighter">New Arrivals</h2>
               <div className="bg-white p-2 rounded-2xl border border-slate-200 flex space-x-2">
                 <button className="px-6 py-2 bg-yellow-500 text-slate-950 rounded-xl font-bold text-sm">All</button>
                 <button className="px-6 py-2 hover:bg-slate-50 rounded-xl font-bold text-sm">Dogs</button>
                 <button className="px-6 py-2 hover:bg-slate-50 rounded-xl font-bold text-sm">Cats</button>
               </div>
             </div>
             
             <div className="grid md:grid-cols-3 gap-8">
               {[
                 { name: "Atlas", breed: "Samoyed", age: "3y", img: "https://images.unsplash.com/photo-1583337130417-3346a1be7dee?auto=format&fit=crop&q=80&w=600" },
                 { name: "Luna", breed: "Husky", age: "1y", img: "https://images.unsplash.com/photo-1537151608828-ea2b11777ee8?auto=format&fit=crop&q=80&w=600" },
                 { name: "Mochi", breed: "Calico", age: "4m", img: "https://images.unsplash.com/photo-1514888286974-6c03e2ca1dba?auto=format&fit=crop&q=80&w=600" }
               ].map((pet, i) => (
                 <div key={i} className="bg-white rounded-[40px] p-6 shadow-sm border border-slate-100 hover:shadow-2xl transition-all group overflow-hidden">
                    <div className="h-72 rounded-[32px] overflow-hidden mb-6 relative">
                      <img src={pet.img} className="w-full h-full object-cover group-hover:scale-110 transition-transform duration-700" alt="" />
                    </div>
                    <div className="space-y-4">
                       <div className="flex justify-between items-center">
                         <h4 className="text-3xl font-black">{pet.name}</h4>
                         <span className="text-yellow-600 font-black">{pet.age}</span>
                       </div>
                       <p className="text-slate-400 font-bold uppercase text-[10px] tracking-widest">{pet.breed}</p>
                       <button className="w-full bg-slate-900 text-white py-4 rounded-2xl font-bold group-hover:bg-yellow-500 transition-all">
                         Meet {pet.name}
                       </button>
                    </div>
                 </div>
               ))}
             </div>
          </div>
        </section>
      )}

      {/* FOOTER */}
      <footer className="bg-slate-950 text-white py-24 border-t border-slate-900">
        <div className="max-w-7xl mx-auto px-6">
          <div className="grid lg:grid-cols-4 gap-16 mb-20">
            <div className="col-span-2 space-y-8">
              <div className="flex items-center space-x-3">
                <div className="w-16 h-16 bg-white/5 rounded-full flex items-center justify-center overflow-hidden border border-white/10">
                   <img src={brandLogo} alt="WPO" className="w-full h-full object-contain scale-[1.7]" />
                </div>
                <span className="text-4xl font-black tracking-tighter uppercase leading-none">PAWFORWARD <br/><span className="text-yellow-500 text-xl font-black">Foundation</span></span>
              </div>
              <p className="text-slate-400 text-xl max-w-lg font-medium leading-relaxed">
                Pioneering the future of animal welfare through blockchain transparency and global community action.
              </p>
            </div>
            <div className="space-y-6">
              <h5 className="font-black uppercase tracking-widest text-yellow-500 text-xs">Foundation</h5>
              <ul className="space-y-4 font-bold text-slate-400">
                <li className="hover:text-yellow-500 cursor-pointer transition-colors">Our Story</li>
                <li className="hover:text-yellow-500 cursor-pointer transition-colors">Shelter Network</li>
                <li className="hover:text-yellow-500 cursor-pointer transition-colors">Transparency Ledger</li>
                <li className="hover:text-yellow-500 cursor-pointer transition-colors">Legal Notice</li>
              </ul>
            </div>
            <div className="space-y-6">
              <h5 className="font-black uppercase tracking-widest text-yellow-500 text-xs">Community</h5>
              <ul className="space-y-4 font-bold text-slate-400">
                <li className="hover:text-yellow-500 cursor-pointer transition-colors">Discord</li>
                <li className="hover:text-yellow-500 cursor-pointer transition-colors">Governance</li>
                <li className="hover:text-yellow-500 cursor-pointer transition-colors">Ambassadors</li>
                <li className="hover:text-yellow-500 cursor-pointer transition-colors">Media Kit</li>
              </ul>
            </div>
          </div>
          <div className="flex flex-col md:flex-row justify-between items-center pt-10 border-t border-slate-900 text-slate-600 text-[10px] font-black uppercase tracking-[0.2em] gap-4">
            <p>© 2026 PawForward Foundation / Non-Profit Org.</p>
            <div className="flex space-x-8">
              <button className="hover:text-white transition-colors">Privacy Policy</button>
              <button className="hover:text-white transition-colors">Terms of Service</button>
            </div>
          </div>
        </div>
      </footer>
    </div>
  );
};

export default App;

