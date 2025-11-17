import React, { useState, useEffect, useCallback, useMemo } from 'react';
import { initializeApp } from 'firebase/app';
import {
  getFirestore, doc, collection, onSnapshot, updateDoc, addDoc, query, getDocs
} from 'firebase/firestore';
import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from 'firebase/auth';
import {
  ShoppingCart, Truck, Users, LayoutDashboard, DollarSign, Package, Zap, X, Menu, Settings, CheckCircle, Clock, ChevronDown, ChevronUp
} from 'lucide-react';

// --- Global Firebase Configuration and Utility Functions ---

// Mandatory global variables for Canvas environment
const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
const firebaseConfig = JSON.parse(typeof __firebase_config !== 'undefined' ? __firebase_config : '{}');
const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

// Utility for exponential backoff (simplified for prototype)
const backoffFetch = async (url, options, retries = 3) => {
  for (let i = 0; i < retries; i++) {
    try {
      const response = await fetch(url, options);
      if (response.ok) return response;
      throw new Error(`HTTP error! status: ${response.status}`);
    } catch (error) {
      if (i === retries - 1) throw error;
      await new Promise(resolve => setTimeout(resolve, Math.pow(2, i) * 1000));
    }
  }
};

// --- App Component Configuration ---

const ROLE_MAP = {
  GMG_ADMIN: 'GMG Admin',
  CLIENT: 'Client (Buyer)',
  DISTRIBUTOR: 'Distributor (Fulfillment)',
};

const VIEWS = {
  GMG_ADMIN: [
    { id: 'Dashboard', name: 'Dashboard', icon: LayoutDashboard },
    { id: 'DistributorManagement', name: 'Distributor Management', icon: Users },
    { id: 'InventoryManagement', name: 'Inventory Management', icon: Package },
    { id: 'OrderOverview', name: 'Order Overview', icon: ShoppingCart },
  ],
  CLIENT: [
    { id: 'Dashboard', name: 'Dashboard', icon: LayoutDashboard },
    { id: 'ProductCatalog', name: 'Product Catalog', icon: ShoppingCart },
    { id: 'MyOrders', name: 'My Orders', icon: Truck },
  ],
  DISTRIBUTOR: [
    { id: 'Dashboard', name: 'Dashboard', icon: LayoutDashboard },
    { id: 'Fulfillment', name: 'Fulfillment & Logistics', icon: Truck },
    { id: 'CreditStatus', name: 'Credit Line Status', icon: DollarSign },
  ],
};

const App = () => {
  const [db, setDb] = useState(null);
  const [auth, setAuth] = useState(null);
  const [userId, setUserId] = useState(null);
  const [isAuthReady, setIsAuthReady] = useState(false);
  const [currentRole, setCurrentRole] = useState('GMG_ADMIN');
  const [currentView, setCurrentView] = useState('Dashboard');
  const [isSidebarOpen, setIsSidebarOpen] = useState(true);

  // Core Data States
  const [inventory, setInventory] = useState([]);
  const [distributors, setDistributors] = useState([]);
  const [orders, setOrders] = useState([]);
  const [message, setMessage] = useState('');

  // Helper for displaying notifications
  const notify = (msg) => {
    setMessage(msg);
    setTimeout(() => setMessage(''), 3000);
  };

  // 1. Firebase Initialization and Authentication
  useEffect(() => {
    if (Object.keys(firebaseConfig).length === 0) {
      console.error("Firebase config is missing. Cannot initialize app.");
      return;
    }

    try {
      const app = initializeApp(firebaseConfig);
      const firestore = getFirestore(app);
      const firebaseAuth = getAuth(app);

      setDb(firestore);
      setAuth(firebaseAuth);

      const signIn = async () => {
        try {
          if (initialAuthToken) {
            await signInWithCustomToken(firebaseAuth, initialAuthToken);
          } else {
            await signInAnonymously(firebaseAuth);
          }
        } catch (e) {
          console.error("Firebase sign-in failed:", e);
        }
      };
      signIn();

      const unsubscribe = onAuthStateChanged(firebaseAuth, (user) => {
        if (user) {
          setUserId(user.uid);
          setIsAuthReady(true);
        } else {
          setUserId(null);
          setIsAuthReady(true);
        }
      });
      return () => unsubscribe();
    } catch (e) {
      console.error("Firebase initialization failed:", e);
    }
  }, []);

  // Set default view when role changes
  useEffect(() => {
    setCurrentView('Dashboard');
  }, [currentRole]);

  // Firestore Collection Paths
  const getAdminPath = (col) => `artifacts/${appId}/users/${userId}/${col}`;
  const getPublicPath = (col) => `artifacts/${appId}/public/data/${col}`;

  // 2. Data Listeners (Real-time updates)
  useEffect(() => {
    if (!isAuthReady || !db || !userId) return;

    // A. Listen to Inventory (Admin's master list for SKUs)
    const inventoryRef = collection(db, getAdminPath('inventory'));
    const unsubInventory = onSnapshot(inventoryRef, (snapshot) => {
      const items = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
      setInventory(items);
    }, (error) => console.error("Error listening to Inventory:", error));

    // B. Listen to Distributors (Admin's management list)
    const distributorRef = collection(db, getAdminPath('distributors'));
    const unsubDistributors = onSnapshot(distributorRef, (snapshot) => {
      const items = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
      setDistributors(items);
    }, (error) => console.error("Error listening to Distributors:", error));

    // C. Listen to Orders (Shared public data)
    const ordersRef = collection(db, getPublicPath('orders'));
    const unsubOrders = onSnapshot(ordersRef, (snapshot) => {
      const items = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
      // Sort orders by creation date (newest first)
      items.sort((a, b) => new Date(b.createdAt) - new Date(a.createdAt));
      setOrders(items);
    }, (error) => console.error("Error listening to Orders:", error));

    // Cleanup listeners on unmount or dependency change
    return () => {
      unsubInventory();
      unsubDistributors();
      unsubOrders();
    };
  }, [isAuthReady, db, userId, getAdminPath, getPublicPath]);

  // 3. Initial Data Seeding (For a fresh start)
  const initializeSKUs = useCallback(async () => {
    if (!db || !userId) return;

    // --- SKU Seeding (50 SKUs) ---
    const inventoryRef = collection(db, getAdminPath('inventory'));
    const inventorySnapshot = await getDocs(query(inventoryRef));

    if (inventorySnapshot.empty) {
      const baseSkus = [
        { name: 'Running Shoes X', category: 'Sport', stock: 120, price: 129.50 },
        { name: 'Protein Powder (Vanilla)', category: 'Health', stock: 500, price: 45.99 },
        { name: 'Smart Aroma Diffuser', category: 'Home', stock: 300, price: 35.00 },
        { name: 'Organic Face Serum', category: 'Beauty', stock: 80, price: 65.00 },
        { name: 'Eco-Friendly Water Bottle', category: 'Everyday', stock: 750, price: 19.99 },
      ];
      const mockSkus = [];
      for (let i = 1; i <= 50; i++) {
        const base = baseSkus[i % baseSkus.length];
        mockSkus.push({
          sku: `${base.category.toUpperCase().substring(0, 4)}-${1000 + i}`,
          name: `${base.name} v${i}`,
          category: base.category,
          stock: base.stock + i * 5,
          price: base.price + (i * 0.5),
        });
      }

      for (const item of mockSkus) {
        await addDoc(inventoryRef, { ...item, adminId: userId });
      }
      notify("Initial 50 SKUs added to inventory.");
    }

    // --- Distributor Seeding (35 Distributors) ---
    const distributorRef = collection(db, getAdminPath('distributors'));
    const distributorSnapshot = await getDocs(query(distributorRef));

    if (distributorSnapshot.empty) {
      const mockDistributors = [];
      for (let i = 1; i <= 35; i++) {
        const baseCredit = 50000 + (i * 2000);
        mockDistributors.push({
          name: `Distributor Co. ${i}`,
          contact: `sales_d${i}@gmgdistro.ae`,
          maxCredit: baseCredit,
          currentCredit: baseCredit,
        });
      }

      for (const dist of mockDistributors) {
        await addDoc(distributorRef, { ...dist, adminId: userId });
      }
      notify("Initial 35 Distributors onboarded.");
    }

    // --- Order Seeding (50 Orders) ---
    const ordersRef = collection(db, getPublicPath('orders'));
    const ordersSnapshot = await getDocs(query(ordersRef));

    if (ordersSnapshot.empty && inventory.length >= 5 && distributors.length >= 5) {
      const mockOrders = [];
      const orderStatuses = ['Pending', 'Fulfilled', 'Shipped'];
      const currentInventory = (await getDocs(query(inventoryRef))).docs.map(doc => ({ id: doc.id, ...doc.data() }));
      const currentDistributors = (await getDocs(query(distributorRef))).docs.map(doc => ({ id: doc.id, ...doc.data() }));

      for (let i = 1; i <= 50; i++) {
        const status = orderStatuses[i % 3];
        const distIndex = i % currentDistributors.length;
        const assignedDistributor = currentDistributors[distIndex];

        // Create 2 to 4 random line items
        const numItems = Math.floor(Math.random() * 3) + 2;
        const items = [];
        let totalCost = 0;
        
        for (let j = 0; j < numItems; j++) {
            const itemIndex = (i + j) % currentInventory.length;
            const item = currentInventory[itemIndex];
            const quantity = Math.floor(Math.random() * 10) + 1; // 1 to 10 quantity
            
            items.push({ 
                sku: item.sku, 
                name: item.name, 
                quantity: quantity, 
                price: item.price 
            });
            totalCost += item.price * quantity;
        }

        mockOrders.push({
          clientId: `CLIENT-${100 + (i % 5)}`, // Mock 5 different clients
          clientName: `Client Buyer ${100 + (i % 5)}`,
          distributorId: assignedDistributor.id,
          distributorName: assignedDistributor.name,
          totalCost: parseFloat(totalCost.toFixed(2)),
          items: items,
          status: status,
          createdAt: new Date(Date.now() - (i * 3600000)).toISOString(), // Spread over time
        });
      }

      for (const order of mockOrders) {
        await addDoc(ordersRef, order);
      }
      notify("Initial 50 Orders seeded.");
    }

  }, [db, userId, getAdminPath, getPublicPath, inventory.length, distributors.length]);

  useEffect(() => {
    if (isAuthReady && userId && db) {
      initializeSKUs();
    }
  }, [isAuthReady, userId, db, initializeSKUs]);

  // --- GMG Admin Functions ---

  const addUpdateDistributor = useCallback(async (data) => {
    if (!db || !userId) return;
    const distributorRef = collection(db, getAdminPath('distributors'));
    try {
      if (data.id) {
        // Update existing
        const docRef = doc(distributorRef, data.id);
        await updateDoc(docRef, {
          name: data.name,
          contact: data.contact,
          maxCredit: parseFloat(data.maxCredit) || 0,
          currentCredit: parseFloat(data.currentCredit) || 0,
        });
        notify(`Distributor ${data.name} updated.`);
      } else {
        // Add new
        await addDoc(distributorRef, {
          name: data.name,
          contact: data.contact,
          maxCredit: parseFloat(data.maxCredit) || 0,
          currentCredit: parseFloat(data.maxCredit) || 0, // Current credit starts as max
          adminId: userId
        });
        notify(`Distributor ${data.name} onboarded.`);
      }
    } catch (e) {
      console.error("Error adding/updating distributor:", e);
      notify("Failed to save distributor.");
    }
  }, [db, userId, getAdminPath]);

  const updateInventory = useCallback(async (item) => {
    if (!db || !userId) return;
    const inventoryRef = collection(db, getAdminPath('inventory'));
    try {
      const docRef = doc(inventoryRef, item.id);
      await updateDoc(docRef, {
        stock: parseInt(item.stock, 10),
        price: parseFloat(item.price),
      });
      notify(`${item.name} stock and price updated.`);
    } catch (e) {
      console.error("Error updating inventory:", e);
      notify("Failed to update inventory.");
    }
  }, [db, userId, getAdminPath]);

  // --- Client Functions ---

  const placeNewOrder = useCallback(async (orderDetails) => {
    if (!db || !userId || !inventory.length || distributors.length === 0) {
      notify("Order failed: App not ready or missing data/distributor configuration.");
      return;
    }

    // 1. Calculate total cost and assign distributor (Round-robin assignment for prototype)
    const totalCost = orderDetails.items.reduce((sum, item) => sum + (item.price * item.quantity), 0);
    
    // Simple round-robin assignment for demonstration
    const lastDistId = localStorage.getItem('lastDistId') || distributors[distributors.length - 1].id;
    const lastIndex = distributors.findIndex(d => d.id === lastDistId);
    const nextIndex = (lastIndex + 1) % distributors.length;
    const assignedDistributor = distributors[nextIndex]; 
    localStorage.setItem('lastDistId', assignedDistributor.id);


    if (totalCost > assignedDistributor.currentCredit) {
      notify(`Order failed: Total cost (${totalCost.toFixed(2)} AED) exceeds Distributor credit line of ${assignedDistributor.currentCredit.toFixed(2)} AED.`);
      return;
    }

    // 2. Create the order document
    const orderRef = collection(db, getPublicPath('orders'));
    try {
      const newOrder = {
        clientId: userId,
        clientName: `Client ${userId.substring(0, 4)}`, // Mock name
        distributorId: assignedDistributor.id,
        distributorName: assignedDistributor.name,
        totalCost: totalCost,
        items: orderDetails.items,
        status: 'Pending',
        createdAt: new Date().toISOString(),
      };
      await addDoc(orderRef, newOrder);

      // 3. Update distributor's current credit (simple deduction)
      const distDocRef = doc(db, getAdminPath('distributors'), assignedDistributor.id);
      await updateDoc(distDocRef, {
        currentCredit: assignedDistributor.currentCredit - totalCost,
      });

      notify(`Order placed successfully! Cost: ${totalCost.toFixed(2)} AED. Credit line updated.`);
    } catch (e) {
      console.error("Error placing order:", e);
      notify("Failed to place order.");
    }
  }, [db, userId, inventory, distributors, getPublicPath, getAdminPath]);

  // --- Distributor Functions ---

  const fulfillOrder = useCallback(async (orderId, newStatus) => {
    if (!db) return;
    const orderDocRef = doc(db, getPublicPath('orders'), orderId);
    try {
      const order = orders.find(o => o.id === orderId);
      if (!order) {
        notify("Order not found.");
        return;
      }

      await updateDoc(orderDocRef, { status: newStatus });

      if (newStatus === 'Fulfilled') {
        // Simple Inventory Deduction (Admin's Master Stock) - for prototype simplicity
        for (const item of order.items) {
          const skuItem = inventory.find(i => i.sku === item.sku);
          if (skuItem) {
            const docRef = doc(db, getAdminPath('inventory'), skuItem.id);
            // Find the current stock item from the state to avoid race condition on immediate update
            const currentStockItem = inventory.find(i => i.id === skuItem.id); 
            if (currentStockItem) {
                await updateDoc(docRef, {
                  stock: currentStockItem.stock - item.quantity,
                });
            }
          }
        }
      }

      notify(`Order ${orderId.substring(0, 4)} status updated to ${newStatus}.`);
    } catch (e) {
      console.error("Error fulfilling order:", e);
      notify("Failed to update order status.");
    }
  }, [db, orders, inventory, getPublicPath, getAdminPath]);

  // --- Utility Components ---

  const getStatusColor = (status) => {
    switch (status) {
      case 'Pending': return 'bg-yellow-100 text-yellow-800 border-yellow-300';
      case 'Fulfilled': return 'bg-green-100 text-green-800 border-green-300';
      case 'Shipped': return 'bg-blue-100 text-blue-800 border-blue-300';
      case 'Cancelled': return 'bg-red-100 text-red-800 border-red-300';
      default: return 'bg-gray-100 text-gray-800 border-gray-300';
    }
  };

  const Card = ({ title, value, icon: Icon, colorClass = 'bg-indigo-500' }) => (
    <div className={`p-5 rounded-xl shadow-lg flex items-center justify-between ${colorClass}`}>
      <div>
        <p className="text-sm font-medium text-white opacity-80">{title}</p>
        <p className="text-3xl font-bold text-white mt-1">{value}</p>
      </div>
      <Icon className="w-8 h-8 text-white opacity-60" />
    </div>
  );

  const OrderDetailList = ({ order }) => (
    <div className="mt-3 p-3 bg-gray-50 border border-gray-200 rounded-lg">
      <p className="text-xs font-semibold uppercase text-gray-600 mb-2">Order Line Items:</p>
      <div className="space-y-1">
        {order.items.map((item, index) => (
          <div key={index} className="flex justify-between text-sm text-gray-800">
            <span className="truncate">{item.name} ({item.sku})</span>
            <span className="font-medium whitespace-nowrap">{item.quantity} x {item.price.toFixed(2)} AED</span>
          </div>
        ))}
      </div>
      <div className="pt-2 mt-2 border-t font-bold flex justify-between">
        <span>Total:</span>
        <span>{order.totalCost.toFixed(2)} AED</span>
      </div>
    </div>
  );

  // --- Sidebar Component ---
  const Sidebar = ({ views, currentView, setCurrentView, isSidebarOpen, setIsSidebarOpen }) => {
    return (
      <div className={`fixed top-0 left-0 h-full bg-white transition-all duration-300 shadow-xl z-20
                      ${isSidebarOpen ? 'w-64' : 'w-20'}`}>
        <div className="p-4 border-b border-indigo-100 flex items-center justify-between h-16">
          <h1 className={`text-xl font-extrabold text-indigo-700 transition-opacity duration-150 ${isSidebarOpen ? 'opacity-100' : 'opacity-0 hidden'}`}>
            GMG Hub
          </h1>
          <button onClick={() => setIsSidebarOpen(!isSidebarOpen)} className="p-2 rounded-full text-indigo-600 hover:bg-indigo-50 transition">
            <Menu className="w-6 h-6" />
          </button>
        </div>

        <nav className="p-2 space-y-2">
          {views.map((view) => (
            <button
              key={view.id}
              onClick={() => setCurrentView(view.id)}
              className={`w-full flex items-center py-3 px-3 rounded-lg transition duration-150
                          ${currentView === view.id
                  ? 'bg-indigo-600 text-white shadow-md'
                  : 'text-gray-600 hover:bg-gray-100 hover:text-indigo-600'
                }`}
            >
              <view.icon className="w-5 h-5 flex-shrink-0" />
              <span className={`ml-3 font-medium whitespace-nowrap transition-opacity duration-150 ${isSidebarOpen ? 'opacity-100' : 'opacity-0 hidden'}`}>
                {view.name}
              </span>
            </button>
          ))}
        </nav>
        <div className="absolute bottom-4 left-0 w-full p-4">
          <p className={`text-xs text-gray-400 truncate ${isSidebarOpen ? 'ml-0' : 'hidden'}`}>User ID: {userId}</p>
        </div>
      </div>
    );
  };


  // --- Views ---

  // --- GMG Admin View Functions ---

  const DistributorManagement = () => {
    const [modalOpen, setModalOpen] = useState(false);
    const [editDistributor, setEditDistributor] = useState(null);

    const openModal = (dist = null) => {
      setEditDistributor(dist);
      setModalOpen(true);
    };

    const handleSave = (data) => {
      addUpdateDistributor(data);
      setModalOpen(false);
    };

    const Modal = ({ dist, onClose, onSave }) => {
      const [name, setName] = useState(dist?.name || '');
      const [contact, setContact] = useState(dist?.contact || '');
      const [maxCredit, setMaxCredit] = useState(dist?.maxCredit || 0);
      const [currentCredit, setCurrentCredit] = useState(dist?.currentCredit || 0);

      const handleSubmit = (e) => {
        e.preventDefault();
        onSave({ id: dist?.id, name, contact, maxCredit, currentCredit });
      };

      return (
        <div className="fixed inset-0 bg-black bg-opacity-50 flex justify-center items-center z-50">
          <div className="bg-white p-6 rounded-xl shadow-2xl w-full max-w-md">
            <div className="flex justify-between items-center mb-4">
              <h3 className="text-xl font-bold text-gray-800">{dist ? 'Edit Distributor & Credit' : 'Onboard New Distributor'}</h3>
              <button onClick={onClose} className="text-gray-400 hover:text-red-600"><X /></button>
            </div>
            <form onSubmit={handleSubmit} className="space-y-4">
              <div>
                <label className="block text-sm font-medium text-gray-700">Name</label>
                <input type="text" value={name} onChange={e => setName(e.target.value)} required className="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border" />
              </div>
              <div>
                <label className="block text-sm font-medium text-gray-700">Contact Email/Phone</label>
                <input type="text" value={contact} onChange={e => setContact(e.target.value)} required className="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border" />
              </div>
              <div>
                <label className="block text-sm font-medium text-gray-700">Max Credit Line (AED)</label>
                <input type="number" step="0.01" min="0" value={maxCredit} onChange={e => setMaxCredit(parseFloat(e.target.value))} required className="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border" />
              </div>
              {dist && (
                <div>
                  <label className="block text-sm font-medium text-gray-700">Current Available Credit (AED)</label>
                  <input type="number" step="0.01" value={currentCredit} onChange={e => setCurrentCredit(parseFloat(e.target.value))} required className="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border" />
                  <p className="text-xs text-gray-500 mt-1">Adjusting this refills or uses up current credit. Max is {maxCredit.toFixed(2)} AED.</p>
                </div>
              )}
              <div className="pt-4 flex justify-end">
                <button type="submit" className="px-4 py-2 bg-indigo-600 text-white font-semibold rounded-lg hover:bg-indigo-700 transition duration-150">
                  {dist ? 'Update Credit & Info' : 'Onboard Distributor'}
                </button>
              </div>
            </form>
          </div>
        </div>
      );
    };

    return (
      <div className="bg-white p-6 rounded-xl shadow-lg">
        <div className="flex justify-between items-center mb-4">
          <h2 className="text-2xl font-semibold text-gray-800 flex items-center"><Users className="mr-2" /> Distributor Management</h2>
          <button onClick={() => openModal(null)} className="flex items-center px-4 py-2 bg-green-500 text-white rounded-lg hover:bg-green-600 transition">
            Onboard New
          </button>
        </div>
        <div className="overflow-x-auto">
          <table className="min-w-full divide-y divide-gray-200">
            <thead className="bg-gray-50">
              <tr>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Distributor</th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Contact</th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Max Credit (AED)</th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Available Credit (AED)</th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Actions</th>
              </tr>
            </thead>
            <tbody className="bg-white divide-y divide-gray-200">
              {distributors.length === 0 ? (
                <tr><td colSpan="5" className="px-6 py-4 text-sm text-gray-500 text-center">No distributors onboarded yet.</td></tr>
              ) : (
                distributors.map(dist => (
                  <tr key={dist.id}>
                    <td className="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">{dist.name}</td>
                    <td className="px-6 py-4 whitespace-nowrap text-sm text-indigo-600 hover:text-indigo-800">
                        <a href={`mailto:${dist.contact}`} className="transition duration-150">
                            {dist.contact}
                        </a>
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{dist.maxCredit.toFixed(2)} AED</td>
                    <td className="px-6 py-4 whitespace-nowrap text-sm">
                      <span className={`font-bold ${dist.currentCredit < dist.maxCredit * 0.2 ? 'text-red-500' : 'text-green-600'}`}>
                        {dist.currentCredit.toFixed(2)} AED
                      </span>
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap text-sm font-medium">
                      <button onClick={() => openModal(dist)} className="text-indigo-600 hover:text-indigo-900 transition">
                        Edit / Manage Credit
                      </button>
                    </td>
                  </tr>
                ))
              )}
            </tbody>
          </table>
        </div>
        {modalOpen && <Modal dist={editDistributor} onClose={() => setModalOpen(false)} onSave={handleSave} />}
      </div>
    );
  };

  const InventoryManagement = () => {
    const [newStock, setNewStock] = useState({});
    const [newPrice, setNewPrice] = useState({});

    useEffect(() => {
      const initialStock = {};
      const initialPrice = {};
      inventory.forEach(item => {
        initialStock[item.id] = item.stock;
        initialPrice[item.id] = item.price;
      });
      setNewStock(initialStock);
      setNewPrice(initialPrice);
    }, [inventory]);

    const handleUpdate = (item) => {
      const stock = newStock[item.id];
      const price = newPrice[item.id];
      if (stock !== undefined && price !== undefined) {
        updateInventory({ id: item.id, stock, price });
      }
    };

    return (
      <div className="bg-white p-6 rounded-xl shadow-lg">
        <h2 className="text-2xl font-semibold text-gray-800 flex items-center mb-4"><Package className="mr-2" /> SKU & Inventory Management ({inventory.length} SKUs)</h2>
        <div className="overflow-x-auto">
          <table className="min-w-full divide-y divide-gray-200">
            <thead className="bg-gray-50">
              <tr>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">SKU</th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Product Name</th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Category</th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Stock</th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Price (AED)</th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Action</th>
              </tr>
            </thead>
            <tbody className="bg-white divide-y divide-gray-200">
              {inventory.map(item => (
                <tr key={item.id}>
                  <td className="px-6 py-4 whitespace-nowrap text-sm font-mono text-gray-600">{item.sku}</td>
                  <td className="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">{item.name}</td>
                  <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{item.category}</td>
                  <td className="px-6 py-4 whitespace-nowrap">
                    <input
                      type="number"
                      value={newStock[item.id] || item.stock}
                      onChange={(e) => setNewStock({ ...newStock, [item.id]: parseInt(e.target.value, 10) })}
                      className="w-20 text-center rounded-md border-gray-300 shadow-sm p-1 border"
                      min="0"
                    />
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap">
                    <input
                      type="number"
                      step="0.01"
                      value={newPrice[item.id] || item.price}
                      onChange={(e) => setNewPrice({ ...newPrice, [item.id]: parseFloat(e.target.value) })}
                      className="w-20 text-center rounded-md border-gray-300 shadow-sm p-1 border"
                      min="0.01"
                    />
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-sm font-medium">
                    <button onClick={() => handleUpdate(item)} className="px-3 py-1 bg-indigo-500 text-white rounded-md hover:bg-indigo-600 transition">
                      Save
                    </button>
                  </td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      </div>
    );
  };

  const OrderOverview = () => {
    const [expandedOrderId, setExpandedOrderId] = useState(null);
    const adminOrders = orders;

    const toggleDetails = (orderId) => {
      setExpandedOrderId(expandedOrderId === orderId ? null : orderId);
    };

    return (
      <div className="bg-white p-6 rounded-xl shadow-lg">
        <h2 className="text-2xl font-semibold text-gray-800 flex items-center mb-4"><ShoppingCart className="mr-2" /> All Orders Overview</h2>
        <div className="space-y-4">
          {adminOrders.length === 0 ? (
            <p className="text-gray-500">No orders placed yet.</p>
          ) : (
            adminOrders.map(order => (
              <div key={order.id} className="border rounded-lg shadow-sm">
                <div 
                  className={`p-4 flex justify-between items-center cursor-pointer transition duration-150 ${expandedOrderId === order.id ? 'bg-indigo-50' : 'hover:bg-gray-50'}`}
                  onClick={() => toggleDetails(order.id)}
                >
                  <div className="flex-1 min-w-0">
                    <div className="flex items-center space-x-2">
                        <p className="text-gray-900 font-bold truncate">Order ID: {order.id.substring(0, 8)}...</p>
                        <span className={`px-2 py-0.5 text-xs font-semibold rounded-full border ${getStatusColor(order.status)}`}>
                          {order.status}
                        </span>
                    </div>
                    <p className="text-sm text-gray-600 mt-1 truncate">Client: {order.clientName} | Distributor: {order.distributorName}</p>
                  </div>
                  <div className="flex items-center space-x-4">
                    <p className="text-lg font-extrabold text-indigo-600 whitespace-nowrap">{order.totalCost.toFixed(2)} AED</p>
                    {expandedOrderId === order.id ? <ChevronUp className="w-5 h-5 text-indigo-600" /> : <ChevronDown className="w-5 h-5 text-gray-500" />}
                  </div>
                </div>
                {expandedOrderId === order.id && <OrderDetailList order={order} />}
              </div>
            ))
          )}
        </div>
      </div>
    );
  };

  const GMGAdminDashboard = () => {
    const totalDistributors = distributors.length;
    const totalSkus = inventory.length;
    const pendingOrders = orders.filter(o => o.status === 'Pending').length;
    const totalCredit = distributors.reduce((sum, d) => sum + d.maxCredit, 0);

    return (
      <div className="space-y-6">
        <h1 className="text-3xl font-extrabold text-indigo-800">GMG Global Operations Dashboard</h1>
        <div className="grid grid-cols-1 md:grid-cols-4 gap-4">
          <Card title="Total Distributors" value={totalDistributors} icon={Users} colorClass="bg-indigo-600" />
          <Card title="Total SKUs Managed" value={totalSkus} icon={Package} colorClass="bg-green-600" />
          <Card title="Pending Orders" value={pendingOrders} icon={Clock} colorClass="bg-yellow-600" />
          <Card title="Total Credit Exposure" value={`${totalCredit.toLocaleString(undefined, { maximumFractionDigits: 0 })} AED`} icon={DollarSign} colorClass="bg-red-600" />
        </div>

        <h2 className="text-2xl font-semibold text-gray-800 pt-4">Quick Links</h2>
        <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
          <button
            onClick={() => setCurrentView('DistributorManagement')}
            className="p-4 bg-white rounded-xl shadow-md hover:shadow-lg transition flex items-center justify-between border-l-4 border-indigo-500"
          >
            <span className="font-medium text-gray-700">Manage Distributors</span>
            <Users className="text-indigo-500" />
          </button>
          <button
            onClick={() => setCurrentView('InventoryManagement')}
            className="p-4 bg-white rounded-xl shadow-md hover:shadow-lg transition flex items-center justify-between border-l-4 border-green-500"
          >
            <span className="font-medium text-gray-700">Update Master Inventory</span>
            <Package className="text-green-500" />
          </button>
          <button
            onClick={() => setCurrentView('OrderOverview')}
            className="p-4 bg-white rounded-xl shadow-md hover:shadow-lg transition flex items-center justify-between border-l-4 border-yellow-500"
          >
            <span className="font-medium text-gray-700">Review All Orders</span>
            <ShoppingCart className="text-yellow-500" />
          </button>
        </div>
      </div>
    );
  };

  // --- Client View Functions ---

  const ProductCatalog = () => {
    const [cart, setCart] = useState({});

    const updateCart = (sku, quantity) => {
      setCart(prev => {
        const newCart = { ...prev };
        const parsedQuantity = parseInt(quantity, 10);
        if (parsedQuantity > 0) {
          newCart[sku] = parsedQuantity;
        } else {
          delete newCart[sku];
        }
        return newCart;
      });
    };

    const cartItems = useMemo(() => {
      return Object.entries(cart).map(([sku, quantity]) => {
        const item = inventory.find(i => i.sku === sku);
        return item ? { ...item, quantity } : null;
      }).filter(Boolean);
    }, [cart, inventory]);

    const totalCost = cartItems.reduce((sum, item) => sum + item.price * item.quantity, 0);

    const handlePlaceOrder = () => {
      if (cartItems.length === 0) {
        notify("Your cart is empty!");
        return;
      }
      placeNewOrder({ items: cartItems.map(i => ({ sku: i.sku, name: i.name, quantity: i.quantity, price: i.price })) });
      setCart({});
    };

    return (
      <div className="space-y-6">
        <div className="bg-indigo-50 p-6 rounded-xl shadow-lg">
          <h2 className="text-2xl font-semibold text-indigo-800 mb-4 flex items-center"><Zap className="mr-2" /> Current Order Cart</h2>
          {cartItems.length === 0 ? (
            <p className="text-indigo-600">Your cart is empty. Add some products!</p>
          ) : (
            <div className="space-y-3">
              {cartItems.map(item => (
                <div key={item.sku} className="flex justify-between items-center border-b pb-2">
                  <p className="text-gray-800">{item.name} x {item.quantity}</p>
                  <p className="font-bold text-gray-900">{(item.price * item.quantity).toFixed(2)} AED</p>
                </div>
              ))}
              <div className="flex justify-between items-center pt-3 border-t-2 border-indigo-200">
                <p className="text-xl font-bold text-indigo-800">Order Total:</p>
                <p className="text-2xl font-extrabold text-indigo-600">{totalCost.toFixed(2)} AED</p>
              </div>
              <button
                onClick={handlePlaceOrder}
                className="w-full py-3 mt-4 bg-green-600 text-white font-bold rounded-lg hover:bg-green-700 transition"
                disabled={distributors.length === 0}
              >
                Place Order
              </button>
            </div>
          )}
        </div>

        <div className="bg-white p-6 rounded-xl shadow-lg">
          <h2 className="text-2xl font-semibold text-gray-800 mb-4">Browse Products</h2>
          <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
            {inventory.map(item => (
              <div key={item.id} className="p-4 border rounded-lg shadow-sm hover:shadow-md transition">
                <p className="text-lg font-bold text-gray-900">{item.name}</p>
                <p className="text-sm text-gray-500 mb-2">{item.category} | SKU: {item.sku}</p>
                <p className="text-xl font-extrabold text-green-600 mb-2">{item.price.toFixed(2)} AED</p>
                <p className={`text-xs font-semibold ${item.stock < 50 ? 'text-red-500' : 'text-green-500'}`}>
                  Stock: {item.stock} in warehouse
                </p>
                <div className="flex items-center mt-3 space-x-2">
                  <input
                    type="number"
                    min="0"
                    max={item.stock}
                    value={cart[item.sku] || 0}
                    onChange={(e) => updateCart(item.sku, e.target.value)}
                    className="w-16 p-1 border rounded-md text-center"
                  />
                  <button
                    onClick={() => updateCart(item.sku, (cart[item.sku] || 0) + 1)}
                    disabled={item.stock <= (cart[item.sku] || 0)}
                    className="px-3 py-1 bg-indigo-500 text-white rounded-md hover:bg-indigo-600 disabled:bg-gray-400 transition"
                  >
                    Add
                  </button>
                </div>
              </div>
            ))}
          </div>
        </div>
      </div>
    );
  };

  const MyOrders = () => {
    const [expandedOrderId, setExpandedOrderId] = useState(null);
    const clientOrders = orders.filter(o => o.clientId === userId);
    
    const toggleDetails = (orderId) => {
      setExpandedOrderId(expandedOrderId === orderId ? null : orderId);
    };

    return (
      <div className="bg-white p-6 rounded-xl shadow-lg">
        <h2 className="text-2xl font-semibold text-gray-800 mb-4">My Orders History</h2>
        <div className="space-y-4">
          {clientOrders.length === 0 ? (
            <p className="text-gray-500">You have no past orders. Place an order in the Product Catalog.</p>
          ) : (
            clientOrders.map(order => (
              <div key={order.id} className="border rounded-lg shadow-sm">
                <div 
                  className={`p-4 flex justify-between items-center cursor-pointer transition duration-150 ${expandedOrderId === order.id ? 'bg-indigo-50' : 'hover:bg-gray-50'}`}
                  onClick={() => toggleDetails(order.id)}
                >
                  <div className="flex-1 min-w-0">
                    <div className="flex items-center space-x-2">
                        <p className="text-gray-900 font-bold truncate">Order ID: {order.id.substring(0, 8)}...</p>
                        <span className={`px-2 py-0.5 text-xs font-semibold rounded-full border ${getStatusColor(order.status)}`}>
                          {order.status}
                        </span>
                    </div>
                    <p className="text-sm text-gray-600 mt-1 truncate">Distributor: {order.distributorName} | Items: {order.items.length}</p>
                  </div>
                  <div className="flex items-center space-x-4">
                    <p className="text-lg font-extrabold text-indigo-600 whitespace-nowrap">{order.totalCost.toFixed(2)} AED</p>
                    {expandedOrderId === order.id ? <ChevronUp className="w-5 h-5 text-indigo-600" /> : <ChevronDown className="w-5 h-5 text-gray-500" />}
                  </div>
                </div>
                {expandedOrderId === order.id && <OrderDetailList order={order} />}
              </div>
            ))
          )}
        </div>
      </div>
    );
  };

  const ClientDashboard = () => {
    const clientOrders = orders.filter(o => o.clientId === userId);
    const pendingCount = clientOrders.filter(o => o.status === 'Pending').length;
    const shippedCount = clientOrders.filter(o => o.status === 'Shipped').length;
    const totalSpent = clientOrders.reduce((sum, o) => sum + o.totalCost, 0);
    const totalProducts = inventory.length;

    return (
      <div className="space-y-6">
        <h1 className="text-3xl font-extrabold text-indigo-800">Client Procurement Dashboard</h1>
        <div className="grid grid-cols-1 md:grid-cols-4 gap-4">
          <Card title="Total Spent YTD" value={`${totalSpent.toFixed(2)} AED`} icon={DollarSign} colorClass="bg-indigo-600" />
          <Card title="Products in Catalog" value={totalProducts} icon={Package} colorClass="bg-green-600" />
          <Card title="Orders Pending" value={pendingCount} icon={Clock} colorClass="bg-yellow-600" />
          <Card title="Orders Shipped" value={shippedCount} icon={CheckCircle} colorClass="bg-blue-600" />
        </div>

        <h2 className="text-2xl font-semibold text-gray-800 pt-4">Start Ordering</h2>
        <div className="grid grid-cols-1 sm:grid-cols-2 gap-4">
          <button
            onClick={() => setCurrentView('ProductCatalog')}
            className="p-6 bg-white rounded-xl shadow-md hover:shadow-lg transition flex items-center justify-between border-b-4 border-indigo-500"
          >
            <span className="text-xl font-medium text-gray-700">Browse Catalog & Place Order</span>
            <ShoppingCart className="text-indigo-500 w-6 h-6" />
          </button>
          <button
            onClick={() => setCurrentView('MyOrders')}
            className="p-6 bg-white rounded-xl shadow-md hover:shadow-lg transition flex items-center justify-between border-b-4 border-green-500"
          >
            <span className="text-xl font-medium text-gray-700">Track My Orders</span>
            <Truck className="text-green-500 w-6 h-6" />
          </button>
        </div>
      </div>
    );
  };

  // --- Distributor View Functions ---

  const Fulfillment = () => {
    const [expandedOrderId, setExpandedOrderId] = useState(null);
    const mockDistributor = distributors[0]; // For prototype, assume session is linked to the first distributor
    const assignedOrders = orders.filter(o => o.distributorId === mockDistributor?.id);
    const pendingOrders = assignedOrders.filter(o => o.status === 'Pending');
    const completedOrders = assignedOrders.filter(o => o.status !== 'Pending');

    const toggleDetails = (orderId) => {
      setExpandedOrderId(expandedOrderId === orderId ? null : orderId);
    };

    if (!mockDistributor) {
      return <p className="text-red-600 p-4 bg-red-100 rounded-lg">No distributor is assigned for this session. Please ensure one is configured by the GMG Admin.</p>;
    }

    return (
      <div className="space-y-6">
        <h2 className="text-2xl font-semibold text-gray-800">Orders Requiring Action ({pendingOrders.length})</h2>
        <div className="grid grid-cols-1 gap-4">
          {pendingOrders.length === 0 ? (
            <p className="col-span-1 text-gray-500 p-4 border rounded-lg">No new orders requiring fulfillment.</p>
          ) : (
            pendingOrders.map(order => (
              <div key={order.id} className="p-4 border border-yellow-300 bg-yellow-50 rounded-lg shadow-sm">
                <div className="flex justify-between items-center cursor-pointer" onClick={() => toggleDetails(order.id)}>
                    <div>
                        <p className="font-bold text-gray-900">Order ID: {order.id.substring(0, 8)}...</p>
                        <p className="text-lg font-extrabold text-indigo-600 my-1">Total: {order.totalCost.toFixed(2)} AED</p>
                        <p className="text-sm text-gray-600">Client: {order.clientName}</p>
                    </div>
                    {expandedOrderId === order.id ? <ChevronUp className="w-5 h-5 text-indigo-600" /> : <ChevronDown className="w-5 h-5 text-gray-500" />}
                </div>

                {expandedOrderId === order.id && <OrderDetailList order={order} />}
                
                <div className="mt-4 flex space-x-2 border-t pt-3">
                  <button
                    onClick={() => fulfillOrder(order.id, 'Fulfilled')}
                    className="flex-1 py-2 bg-green-600 text-white rounded-lg hover:bg-green-700 transition"
                  >
                    Mark as Fulfilled
                  </button>
                  <button
                    onClick={() => fulfillOrder(order.id, 'Cancelled')}
                    className="py-2 px-4 bg-red-500 text-white rounded-lg hover:bg-red-600 transition"
                  >
                    Cancel
                  </button>
                </div>
              </div>
            ))
          )}
        </div>

        <h2 className="text-2xl font-semibold text-gray-800 pt-4">Completed Orders ({completedOrders.length})</h2>
        <div className="space-y-4">
          {completedOrders.map(order => (
            <div key={order.id} className="border rounded-lg shadow-sm">
              <div 
                  className={`p-3 flex justify-between items-center cursor-pointer transition duration-150 ${expandedOrderId === order.id ? 'bg-indigo-50' : 'hover:bg-gray-50'}`}
                  onClick={() => toggleDetails(order.id)}
              >
                  <div>
                    <div className="flex items-center space-x-2">
                        <p className="font-bold text-gray-800">Order ID: {order.id.substring(0, 8)}...</p>
                        <span className={`px-2 py-0.5 text-xs font-semibold rounded-full border ${getStatusColor(order.status)}`}>
                          {order.status}
                        </span>
                    </div>
                    <p className="text-sm text-gray-600">Total: {order.totalCost.toFixed(2)} AED</p>
                  </div>
                  <div className="flex items-center space-x-3">
                    {order.status === 'Fulfilled' && (
                      <button
                        onClick={(e) => {e.stopPropagation(); fulfillOrder(order.id, 'Shipped');}}
                        className="py-1 px-3 bg-blue-500 text-white rounded-md text-sm hover:bg-blue-600 transition"
                      >
                        Mark as Shipped
                      </button>
                    )}
                    {expandedOrderId === order.id ? <ChevronUp className="w-5 h-5 text-indigo-600" /> : <ChevronDown className="w-5 h-5 text-gray-500" />}
                  </div>
              </div>
              {expandedOrderId === order.id && <OrderDetailList order={order} />}
            </div>
          ))}
        </div>
      </div>
    );
  };

  const CreditStatus = () => {
    const mockDistributor = distributors[0];
    if (!mockDistributor) {
      return <p className="text-red-600 p-4 bg-red-100 rounded-lg">No distributor is assigned for this session.</p>;
    }

    const usagePercentage = (mockDistributor.maxCredit - mockDistributor.currentCredit) / mockDistributor.maxCredit * 100;
    
    return (
      <div className="bg-white p-6 rounded-xl shadow-lg space-y-6">
        <h2 className="text-2xl font-semibold text-gray-800">Credit Line Overview for {mockDistributor.name}</h2>

        <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
          <Card title="Maximum Credit Limit" value={`${mockDistributor.maxCredit.toFixed(2)} AED`} icon={DollarSign} colorClass="bg-indigo-600" />
          <Card title="Available Credit" value={`${mockDistributor.currentCredit.toFixed(2)} AED`} icon={DollarSign} colorClass={mockDistributor.currentCredit < mockDistributor.maxCredit * 0.2 ? 'bg-red-600' : 'bg-green-600'} />
          <Card title="Credit Used" value={`${(mockDistributor.maxCredit - mockDistributor.currentCredit).toFixed(2)} AED`} icon={DollarSign} colorClass="bg-yellow-600" />
        </div>

        <div className="space-y-2 pt-4">
          <p className="text-lg font-medium text-gray-700">Credit Usage ({usagePercentage.toFixed(1)}%)</p>
          <div className="w-full bg-gray-200 rounded-full h-4">
            <div
              className={`h-4 rounded-full transition-all duration-500 
                          ${usagePercentage > 80 ? 'bg-red-500' : usagePercentage > 50 ? 'bg-yellow-500' : 'bg-green-500'}`}
              style={{ width: `${usagePercentage}%` }}
            ></div>
          </div>
        </div>

        <p className="text-sm text-gray-500 pt-4">
          Orders from Clients will be denied if they exceed the **Available Credit** line. The GMG Admin can replenish or adjust this credit.
        </p>
      </div>
    );
  };

  const DistributorDashboard = () => {
    const mockDistributor = distributors[0];
    if (!mockDistributor) {
      return <p className="text-red-600 p-4 bg-red-100 rounded-lg">No distributor is assigned for this session. Please ensure one is configured by the GMG Admin.</p>;
    }
    
    const assignedOrders = orders.filter(o => o.distributorId === mockDistributor.id);
    const pendingCount = assignedOrders.filter(o => o.status === 'Pending').length;
    const shippedCount = assignedOrders.filter(o => o.status === 'Shipped').length;
    const fulfilledCount = assignedOrders.filter(o => o.status === 'Fulfilled').length;


    return (
      <div className="space-y-6">
        <h1 className="text-3xl font-extrabold text-indigo-800">Fulfillment Dashboard: {mockDistributor.name}</h1>
        <div className="grid grid-cols-1 md:grid-cols-4 gap-4">
          <Card title="Available Credit" value={`${mockDistributor.currentCredit.toFixed(2)} AED`} icon={DollarSign} colorClass="bg-green-600" />
          <Card title="Orders to Fulfill" value={pendingCount} icon={Clock} colorClass="bg-yellow-600" />
          <Card title="Ready to Ship" value={fulfilledCount} icon={CheckCircle} colorClass="bg-blue-600" />
          <Card title="Completed Shipments" value={shippedCount} icon={Truck} colorClass="bg-indigo-600" />
        </div>

        <h2 className="text-2xl font-semibold text-gray-800 pt-4">Fulfillment Queue</h2>
        <div className="grid grid-cols-1 sm:grid-cols-2 gap-4">
          <button
            onClick={() => setCurrentView('Fulfillment')}
            className="p-6 bg-white rounded-xl shadow-md hover:shadow-lg transition flex items-center justify-between border-b-4 border-yellow-500"
          >
            <span className="text-xl font-medium text-gray-700">Process Pending Orders</span>
            <Clock className="text-yellow-500 w-6 h-6" />
          </button>
          <button
            onClick={() => setCurrentView('CreditStatus')}
            className="p-6 bg-white rounded-xl shadow-md hover:shadow-lg transition flex items-center justify-between border-b-4 border-green-500"
          >
            <span className="text-xl font-medium text-gray-700">View Credit Line Details</span>
            <DollarSign className="text-green-500 w-6 h-6" />
          </button>
        </div>
      </div>
    );
  };

  // --- Main Render Function ---

  const renderContent = () => {
    switch (currentRole) {
      case 'GMG_ADMIN':
        switch (currentView) {
          case 'Dashboard': return <GMGAdminDashboard />;
          case 'DistributorManagement': return <DistributorManagement />;
          case 'InventoryManagement': return <InventoryManagement />;
          case 'OrderOverview': return <OrderOverview />;
          default: return <GMGAdminDashboard />;
        }
      case 'CLIENT':
        switch (currentView) {
          case 'Dashboard': return <ClientDashboard />;
          case 'ProductCatalog': return <ProductCatalog />;
          case 'MyOrders': return <MyOrders />;
          default: return <ClientDashboard />;
        }
      case 'DISTRIBUTOR':
        // For prototype, we link the distributor to the first entry in the list
        const distributorKey = distributors.length > 0 ? distributors[0].id : 'N/A';
        const roleDistributor = distributors.find(d => d.id === distributorKey);

        switch (currentView) {
          case 'Dashboard': return <DistributorDashboard />;
          case 'Fulfillment': return <Fulfillment />;
          case 'CreditStatus': return <CreditStatus />;
          default: return <DistributorDashboard />;
        }
      default:
        return <p className="text-center text-xl text-gray-600">Select a role to begin.</p>;
    }
  };

  if (!isAuthReady) {
    return (
      <div className="flex items-center justify-center min-h-screen bg-gray-50">
        <div className="text-xl font-semibold text-gray-600">Initializing Application and Authentication...</div>
      </div>
    );
  }

  const sidebarWidthClass = isSidebarOpen ? 'ml-64' : 'ml-20';

  return (
    <div className="min-h-screen bg-gray-50 font-sans">
      {/* Fixed Header/Role Selector */}
      <div className={`fixed top-0 right-0 bg-white shadow-md z-30 p-4 flex justify-end items-center h-16 transition-all duration-300 ${sidebarWidthClass} w-auto`}>
        <p className="font-semibold text-gray-700 mr-2 text-sm">Role:</p>
        <select
          value={currentRole}
          onChange={(e) => setCurrentRole(e.target.value)}
          className="p-2 border border-gray-300 rounded-lg focus:ring-indigo-500 focus:border-indigo-500 transition text-sm"
        >
          {Object.entries(ROLE_MAP).map(([key, value]) => (
            <option key={key} value={key}>{value}</option>
          ))}
        </select>
      </div>

      {/* Sidebar */}
      <Sidebar
        views={VIEWS[currentRole]}
        currentView={currentView}
        setCurrentView={setCurrentView}
        isSidebarOpen={isSidebarOpen}
        setIsSidebarOpen={setIsSidebarOpen}
      />

      {/* Main Content Area */}
      <main className={`p-4 sm:p-8 pt-20 transition-all duration-300 min-h-screen ${sidebarWidthClass}`}>
        <h1 className="text-3xl font-extrabold text-gray-800 mb-6 border-b pb-2">{VIEWS[currentRole].find(v => v.id === currentView)?.name || 'Dashboard'}</h1>
        {renderContent()}
      </main>

      {/* Global Notification/Message Box */}
      {message && (
        <div className="fixed bottom-4 right-4 bg-indigo-600 text-white p-3 rounded-lg shadow-xl transition-opacity duration-300 z-50">
          {message}
        </div>
      )}
    </div>
  );
};

export default App;
