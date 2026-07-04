import { useState, useEffect, useRef } from "react";

const COLORS = {
  obsidian: "#0A0A0F",
  slate: "#1C1C2E",
  violet: "#6C3FE8",
  violetLight: "#8B5CF6",
  gold: "#D4AF6A",
  goldLight: "#F0D080",
  white: "#F8F6FF",
  muted: "#9090B0",
  card: "rgba(28,28,46,0.85)",
  glass: "rgba(108,63,232,0.12)",
  success: "#22C55E",
  danger: "#EF4444",
  warning: "#F59E0B",
};

const products = [
  { id: 1, name: "Silk Aurora Saree", price: 4299, mrp: 7999, seller: "LuxeWeaves", rating: 4.8, reviews: 2341, category: "Fashion", image: "🥻", badge: "Bestseller", aiScore: 98, stock: 12, deliveryDays: 2 },
  { id: 2, name: "Pro Gaming Laptop X1", price: 89999, mrp: 119999, seller: "TechVault", rating: 4.9, reviews: 8732, category: "Electronics", image: "💻", badge: "AI Pick", aiScore: 96, stock: 5, deliveryDays: 1 },
  { id: 3, name: "Organic Saffron 10g", price: 849, mrp: 1299, seller: "SpiceRoute", rating: 4.7, reviews: 5621, category: "Grocery", image: "🌸", badge: "Fresh", aiScore: 92, stock: 87, deliveryDays: 3 },
  { id: 4, name: "Diamond Necklace Set", price: 24999, mrp: 38000, seller: "JewelCraft", rating: 4.9, reviews: 1203, category: "Jewelry", image: "💎", badge: "Luxury", aiScore: 95, stock: 3, deliveryDays: 2 },
  { id: 5, name: "Ergonomic Office Chair", price: 12499, mrp: 18999, seller: "ComfortZone", rating: 4.6, reviews: 4521, category: "Furniture", image: "🪑", badge: "Top Rated", aiScore: 89, stock: 20, deliveryDays: 5 },
  { id: 6, name: "Artisan Coffee Blend", price: 699, mrp: 999, seller: "BeanMaster", rating: 4.8, reviews: 9876, category: "Grocery", image: "☕", badge: "Daily Deal", aiScore: 91, stock: 150, deliveryDays: 2 },
  { id: 7, name: "Wireless Earbuds Pro", price: 6999, mrp: 11999, seller: "SoundWave", rating: 4.7, reviews: 12043, category: "Electronics", image: "🎧", badge: "Flash Sale", aiScore: 94, stock: 34, deliveryDays: 1 },
  { id: 8, name: "Handwoven Carpet 5×7", price: 8799, mrp: 14999, seller: "CraftHaven", rating: 4.5, reviews: 876, category: "Home", image: "🏠", badge: "Artisan", aiScore: 87, stock: 8, deliveryDays: 7 },
];

const categories = [
  { name: "Electronics", icon: "⚡", color: "#6C3FE8", count: "2.4M+" },
  { name: "Fashion", icon: "👗", color: "#EC4899", count: "8.1M+" },
  { name: "Grocery", icon: "🛒", color: "#22C55E", count: "1.2M+" },
  { name: "Jewelry", icon: "💍", color: "#D4AF6A", count: "450K+" },
  { name: "Home", icon: "🏡", color: "#F59E0B", count: "3.3M+" },
  { name: "Beauty", icon: "✨", color: "#A855F7", count: "900K+" },
  { name: "Sports", icon: "🏋️", color: "#EF4444", count: "670K+" },
  { name: "Books", icon: "📚", color: "#06B6D4", count: "5M+" },
];

const orders = [
  { id: "#LM2024001", product: "Pro Gaming Laptop X1", status: "Out for Delivery", progress: 85, location: "Mumbai - 2km away", eta: "Today, 4:30 PM", steps: ["Ordered", "Packed", "Shipped", "In Transit", "Delivered"], current: 3 },
  { id: "#LM2024002", product: "Silk Aurora Saree", status: "Shipped", progress: 55, location: "Pune Sorting Hub", eta: "Tomorrow, 6:00 PM", steps: ["Ordered", "Packed", "Shipped", "In Transit", "Delivered"], current: 2 },
  { id: "#LM2024003", product: "Diamond Necklace Set", status: "Packed", progress: 30, location: "Seller Warehouse", eta: "Dec 28, 2:00 PM", steps: ["Ordered", "Packed", "Shipped", "In Transit", "Delivered"], current: 1 },
];

const sellers = [
  { name: "TechVault", rating: 4.9, products: 1240, sales: "₹2.4Cr", badge: "Top Seller", category: "Electronics" },
  { name: "LuxeWeaves", rating: 4.8, products: 876, sales: "₹89L", badge: "Verified", category: "Fashion" },
  { name: "JewelCraft", rating: 4.9, products: 432, sales: "₹1.2Cr", badge: "Premium", category: "Jewelry" },
  { name: "SpiceRoute", rating: 4.7, products: 234, sales: "₹45L", badge: "Organic", category: "Grocery" },
];

const heroSlides = [
  { title: "Year-End Mega Sale", subtitle: "Up to 80% off on Premium Brands", cta: "Shop Now", gradient: "linear-gradient(135deg, #6C3FE8 0%, #1C1C2E 100%)", accent: "#D4AF6A" },
  { title: "AI-Curated For You", subtitle: "Personalized picks just for your taste", cta: "Explore", gradient: "linear-gradient(135deg, #1C1C2E 0%, #0A0A0F 50%, #2D1B69 100%)", accent: "#8B5CF6" },
  { title: "Seller Marketplace", subtitle: "Start selling to 10M+ customers today", cta: "Become a Seller", gradient: "linear-gradient(135deg, #0A0A0F 0%, #1a0a2e 100%)", accent: "#D4AF6A" },
];

function useAIRecommendations() {
  const [aiResponse, setAiResponse] = useState("");
  const [loading, setLoading] = useState(false);

  const getRecommendation = async (userPref) => {
    setLoading(true);
    setAiResponse("");
    try {
      const response = await fetch("https://api.anthropic.com/v1/messages", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
          model: "claude-sonnet-4-6",
          max_tokens: 1000,
          messages: [{
            role: "user",
            content: `You are LuxeMart's AI Shopping Assistant. The user says: "${userPref}". 
            Available products: ${products.map(p => `${p.name} (₹${p.price}, ${p.category}, rated ${p.rating}★)`).join(", ")}.
            Give 2-3 personalized product recommendations in a warm, concise way. 
            Mention specific products by name, explain why they match, and include a buying tip. Keep it under 120 words. No markdown formatting.`
          }]
        })
      });
      const data = await response.json();
      const text = data.content?.find(b => b.type === "text")?.text || "I'd love to help! Try describing what you're looking for.";
      setAiResponse(text);
    } catch {
      setAiResponse("Our AI stylist is taking a break. Browse our curated collections below!");
    }
    setLoading(false);
  };

  return { aiResponse, loading, getRecommendation };
}

function StarRating({ rating }) {
  return (
    <span style={{ color: COLORS.gold, fontSize: 12 }}>
      {"★".repeat(Math.floor(rating))}{"☆".repeat(5 - Math.floor(rating))}
      <span style={{ color: COLORS.muted, marginLeft: 4 }}>{rating}</span>
    </span>
  );
}

function Badge({ text, color = COLORS.violet }) {
  return (
    <span style={{
      background: color + "22",
      color: color,
      border: `1px solid ${color}44`,
      borderRadius: 20,
      padding: "2px 10px",
      fontSize: 11,
      fontWeight: 700,
      letterSpacing: 0.5,
    }}>{text}</span>
  );
}

function ProductCard({ product, onAddCart }) {
  const [hovered, setHovered] = useState(false);
  const discount = Math.round((1 - product.price / product.mrp) * 100);

  return (
    <div
      onMouseEnter={() => setHovered(true)}
      onMouseLeave={() => setHovered(false)}
      style={{
        background: hovered ? "rgba(108,63,232,0.15)" : COLORS.card,
        border: `1px solid ${hovered ? COLORS.violet + "66" : "#2a2a4a"}`,
        borderRadius: 16,
        padding: 20,
        cursor: "pointer",
        transition: "all 0.3s ease",
        transform: hovered ? "translateY(-4px)" : "none",
        boxShadow: hovered ? `0 12px 40px ${COLORS.violet}33` : "0 2px 12px rgba(0,0,0,0.4)",
        backdropFilter: "blur(10px)",
        position: "relative",
        overflow: "hidden",
      }}
    >
      {hovered && (
        <div style={{
          position: "absolute", top: 0, left: 0, right: 0, height: 2,
          background: `linear-gradient(90deg, transparent, ${COLORS.violet}, ${COLORS.gold}, transparent)`,
        }} />
      )}
      <div style={{ display: "flex", justifyContent: "space-between", marginBottom: 8 }}>
        <Badge text={product.badge} color={product.badge === "Luxury" ? COLORS.gold : product.badge === "Flash Sale" ? COLORS.danger : COLORS.violet} />
        <span style={{ fontSize: 11, color: COLORS.muted }}>{product.seller}</span>
      </div>
      <div style={{ fontSize: 48, textAlign: "center", margin: "16px 0", filter: "drop-shadow(0 4px 8px rgba(0,0,0,0.5))" }}>
        {product.image}
      </div>
      <div style={{ color: COLORS.white, fontWeight: 700, fontSize: 14, marginBottom: 6, lineHeight: 1.3 }}>
        {product.name}
      </div>
      <StarRating rating={product.rating} />
      <span style={{ color: COLORS.muted, fontSize: 11, marginLeft: 6 }}>({product.reviews.toLocaleString()})</span>
      <div style={{ marginTop: 12, display: "flex", alignItems: "baseline", gap: 8 }}>
        <span style={{ color: COLORS.white, fontWeight: 800, fontSize: 18 }}>₹{product.price.toLocaleString()}</span>
        <span style={{ color: COLORS.muted, fontSize: 12, textDecoration: "line-through" }}>₹{product.mrp.toLocaleString()}</span>
        <span style={{ color: COLORS.success, fontSize: 12, fontWeight: 700 }}>{discount}% off</span>
      </div>
      <div style={{ color: COLORS.muted, fontSize: 11, marginTop: 4 }}>
        🚚 {product.deliveryDays === 1 ? "Tomorrow" : `${product.deliveryDays} days`} · {product.stock < 10 ? <span style={{ color: COLORS.warning }}>Only {product.stock} left!</span> : `${product.stock} in stock`}
      </div>

      {/* AI Score Bar */}
      <div style={{ marginTop: 12 }}>
        <div style={{ display: "flex", justifyContent: "space-between", fontSize: 10, color: COLORS.muted, marginBottom: 4 }}>
          <span>🤖 AI Match</span>
          <span style={{ color: COLORS.violetLight }}>{product.aiScore}%</span>
        </div>
        <div style={{ height: 3, background: "#2a2a4a", borderRadius: 2 }}>
          <div style={{ width: `${product.aiScore}%`, height: "100%", background: `linear-gradient(90deg, ${COLORS.violet}, ${COLORS.gold})`, borderRadius: 2, transition: "width 0.8s ease" }} />
        </div>
      </div>

      <button
        onClick={() => onAddCart(product)}
        style={{
          marginTop: 14, width: "100%", padding: "10px", borderRadius: 10,
          background: hovered ? `linear-gradient(135deg, ${COLORS.violet}, ${COLORS.violetLight})` : "transparent",
          border: `1px solid ${COLORS.violet}`,
          color: COLORS.white, fontWeight: 700, fontSize: 13, cursor: "pointer",
          transition: "all 0.3s ease",
        }}
      >
        {hovered ? "⚡ Add to Cart" : "Add to Cart"}
      </button>
    </div>
  );
}

function OrderTracker({ order }) {
  return (
    <div style={{ background: COLORS.card, border: "1px solid #2a2a4a", borderRadius: 16, padding: 20, marginBottom: 16 }}>
      <div style={{ display: "flex", justifyContent: "space-between", alignItems: "flex-start", marginBottom: 16 }}>
        <div>
          <div style={{ color: COLORS.white, fontWeight: 700, fontSize: 15 }}>{order.product}</div>
          <div style={{ color: COLORS.muted, fontSize: 12, marginTop: 2 }}>{order.id}</div>
        </div>
        <Badge text={order.status} color={order.status === "Out for Delivery" ? COLORS.success : order.status === "Shipped" ? COLORS.violetLight : COLORS.gold} />
      </div>

      <div style={{ display: "flex", alignItems: "center", gap: 8, marginBottom: 16, background: "rgba(108,63,232,0.1)", borderRadius: 10, padding: "8px 12px" }}>
        <span style={{ fontSize: 16 }}>📍</span>
        <div>
          <div style={{ color: COLORS.white, fontSize: 13, fontWeight: 600 }}>{order.location}</div>
          <div style={{ color: COLORS.gold, fontSize: 11 }}>ETA: {order.eta}</div>
        </div>
      </div>

      <div style={{ display: "flex", justifyContent: "space-between", position: "relative" }}>
        <div style={{ position: "absolute", top: 10, left: "10%", right: "10%", height: 2, background: "#2a2a4a" }} />
        <div style={{
          position: "absolute", top: 10, left: "10%", height: 2,
          width: `${(order.current / (order.steps.length - 1)) * 80}%`,
          background: `linear-gradient(90deg, ${COLORS.violet}, ${COLORS.gold})`,
          transition: "width 1s ease"
        }} />
        {order.steps.map((step, i) => (
          <div key={step} style={{ display: "flex", flexDirection: "column", alignItems: "center", gap: 6, zIndex: 1 }}>
            <div style={{
              width: 20, height: 20, borderRadius: "50%",
              background: i <= order.current ? `linear-gradient(135deg, ${COLORS.violet}, ${COLORS.gold})` : "#2a2a4a",
              border: `2px solid ${i <= order.current ? COLORS.gold : "#3a3a5a"}`,
              display: "flex", alignItems: "center", justifyContent: "center",
              fontSize: 8, color: COLORS.white, fontWeight: 700,
              boxShadow: i === order.current ? `0 0 12px ${COLORS.violet}` : "none",
            }}>{i <= order.current ? "✓" : i + 1}</div>
            <span style={{ fontSize: 9, color: i <= order.current ? COLORS.violetLight : COLORS.muted, textAlign: "center", maxWidth: 50 }}>{step}</span>
          </div>
        ))}
      </div>
    </div>
  );
}

function AIAssistant({ onGetRec }) {
  const [input, setInput] = useState("");
  const { aiResponse, loading, getRecommendation } = useAIRecommendations();

  const suggestions = ["Budget laptop under ₹50k", "Gift for mom", "Healthy snacks", "Home office setup"];

  return (
    <div style={{ background: "linear-gradient(135deg, rgba(108,63,232,0.15), rgba(212,175,106,0.08))", border: `1px solid ${COLORS.violet}44`, borderRadius: 20, padding: 24, marginBottom: 32 }}>
      <div style={{ display: "flex", alignItems: "center", gap: 12, marginBottom: 16 }}>
        <div style={{ width: 42, height: 42, borderRadius: "50%", background: `linear-gradient(135deg, ${COLORS.violet}, ${COLORS.gold})`, display: "flex", alignItems: "center", justifyContent: "center", fontSize: 20, boxShadow: `0 0 20px ${COLORS.violet}66` }}>🤖</div>
        <div>
          <div style={{ color: COLORS.white, fontWeight: 800, fontSize: 16 }}>LuxeMart AI Stylist</div>
          <div style={{ color: COLORS.violetLight, fontSize: 12 }}>Powered by Claude · Personalized for you</div>
        </div>
        <div style={{ marginLeft: "auto", display: "flex", alignItems: "center", gap: 6 }}>
          <div style={{ width: 8, height: 8, borderRadius: "50%", background: COLORS.success, animation: "pulse 2s infinite" }} />
          <span style={{ color: COLORS.success, fontSize: 11 }}>Live</span>
        </div>
      </div>

      <div style={{ display: "flex", gap: 8, marginBottom: 12 }}>
        <input
          value={input}
          onChange={e => setInput(e.target.value)}
          onKeyDown={e => e.key === "Enter" && input && getRecommendation(input)}
          placeholder="Tell me what you're looking for..."
          style={{
            flex: 1, background: "rgba(0,0,0,0.3)", border: `1px solid ${COLORS.violet}44`,
            borderRadius: 10, padding: "10px 16px", color: COLORS.white, fontSize: 14,
            outline: "none",
          }}
        />
        <button
          onClick={() => input && getRecommendation(input)}
          style={{
            padding: "10px 20px", borderRadius: 10, border: "none",
            background: `linear-gradient(135deg, ${COLORS.violet}, ${COLORS.violetLight})`,
            color: COLORS.white, fontWeight: 700, cursor: "pointer", fontSize: 14,
          }}
        >{loading ? "..." : "Ask AI"}</button>
      </div>

      <div style={{ display: "flex", gap: 8, flexWrap: "wrap", marginBottom: aiResponse ? 12 : 0 }}>
        {suggestions.map(s => (
          <button key={s} onClick={() => { setInput(s); getRecommendation(s); }}
            style={{ background: "rgba(108,63,232,0.15)", border: `1px solid ${COLORS.violet}44`, borderRadius: 20, padding: "4px 12px", color: COLORS.violetLight, fontSize: 12, cursor: "pointer" }}>
            {s}
          </button>
        ))}
      </div>

      {loading && (
        <div style={{ display: "flex", gap: 6, padding: "12px 0", alignItems: "center" }}>
          {[0, 1, 2].map(i => (
            <div key={i} style={{ width: 8, height: 8, borderRadius: "50%", background: COLORS.violet, opacity: 0.6, animation: `bounce 1s infinite ${i * 0.2}s` }} />
          ))}
          <span style={{ color: COLORS.muted, fontSize: 13, marginLeft: 4 }}>Finding perfect matches...</span>
        </div>
      )}

      {aiResponse && !loading && (
        <div style={{ background: "rgba(0,0,0,0.3)", borderRadius: 12, padding: 16, marginTop: 8, borderLeft: `3px solid ${COLORS.violet}` }}>
          <div style={{ color: COLORS.muted, fontSize: 11, marginBottom: 6 }}>🤖 AI Stylist says:</div>
          <div style={{ color: COLORS.white, fontSize: 14, lineHeight: 1.6 }}>{aiResponse}</div>
        </div>
      )}
    </div>
  );
}

function SellerDashboard() {
  const [activeTab, setActiveTab] = useState("overview");
  const metrics = [
    { label: "Total Revenue", value: "₹4.2L", change: "+18%", icon: "💰" },
    { label: "Orders Today", value: "247", change: "+34%", icon: "📦" },
    { label: "Products Live", value: "1,240", change: "+12", icon: "🏷️" },
    { label: "Avg Rating", value: "4.8★", change: "+0.2", icon: "⭐" },
  ];

  return (
    <div>
      <div style={{ display: "grid", gridTemplateColumns: "repeat(2, 1fr)", gap: 16, marginBottom: 24 }}>
        {metrics.map(m => (
          <div key={m.label} style={{ background: COLORS.card, border: "1px solid #2a2a4a", borderRadius: 14, padding: 16 }}>
            <div style={{ display: "flex", justifyContent: "space-between", alignItems: "flex-start" }}>
              <div>
                <div style={{ color: COLORS.muted, fontSize: 12, marginBottom: 6 }}>{m.label}</div>
                <div style={{ color: COLORS.white, fontWeight: 800, fontSize: 20 }}>{m.value}</div>
              </div>
              <span style={{ fontSize: 24 }}>{m.icon}</span>
            </div>
            <div style={{ color: COLORS.success, fontSize: 12, marginTop: 8, fontWeight: 600 }}>{m.change} this week</div>
          </div>
        ))}
      </div>
      <div style={{ background: COLORS.card, border: "1px solid #2a2a4a", borderRadius: 14, padding: 20 }}>
        <div style={{ color: COLORS.white, fontWeight: 700, marginBottom: 16 }}>Top Sellers on Platform</div>
        {sellers.map((s, i) => (
          <div key={s.name} style={{ display: "flex", alignItems: "center", gap: 12, padding: "10px 0", borderBottom: i < sellers.length - 1 ? "1px solid #2a2a4a" : "none" }}>
            <div style={{ width: 36, height: 36, borderRadius: "50%", background: `linear-gradient(135deg, ${COLORS.violet}, ${COLORS.gold})`, display: "flex", alignItems: "center", justifyContent: "center", color: COLORS.white, fontWeight: 800, fontSize: 14 }}>{s.name[0]}</div>
            <div style={{ flex: 1 }}>
              <div style={{ color: COLORS.white, fontWeight: 600, fontSize: 14 }}>{s.name}</div>
              <div style={{ color: COLORS.muted, fontSize: 11 }}>{s.category} · {s.products} products</div>
            </div>
            <div style={{ textAlign: "right" }}>
              <div style={{ color: COLORS.gold, fontWeight: 700, fontSize: 13 }}>{s.sales}</div>
              <Badge text={s.badge} color={s.badge === "Top Seller" ? COLORS.gold : COLORS.violetLight} />
            </div>
          </div>
        ))}
      </div>
    </div>
  );
}

export default function LuxeMart() {
  const [activeNav, setActiveNav] = useState("home");
  const [cart, setCart] = useState([]);
  const [cartOpen, setCartOpen] = useState(false);
  const [heroIndex, setHeroIndex] = useState(0);
  const [search, setSearch] = useState("");
  const [selectedCat, setSelectedCat] = useState("All");
  const [notification, setNotification] = useState(null);
  const [filterPrice, setFilterPrice] = useState("all");

  useEffect(() => {
    const t = setInterval(() => setHeroIndex(i => (i + 1) % heroSlides.length), 4000);
    return () => clearInterval(t);
  }, []);

  const showNotification = (msg, type = "success") => {
    setNotification({ msg, type });
    setTimeout(() => setNotification(null), 3000);
  };

  const addToCart = (product) => {
    setCart(prev => {
      const existing = prev.find(i => i.id === product.id);
      if (existing
