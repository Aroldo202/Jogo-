# Jogo-import React, { useMemo, useState, useEffect } from "react";
import { motion } from "framer-motion";

// Formatação BRL
const brl = (value) =>
  new Intl.NumberFormat("pt-BR", { style: "currency", currency: "BRL" }).format(
    value
  );

// Dados do jogo
const MATCH = {
  home: "Corinthians",
  away: "Flamengo",
  dateISO: "2025-09-28T16:00:00-03:00",
  stadium: "Neo Química Arena — São Paulo/SP",
};

// Setores
const SECTORS = [
  { id: "norte", label: "Arquibancada Norte", price: 63.9, short: "Norte" },
  { id: "sul", label: "Arquibancada Sul", price: 75.0, short: "Sul" },
  { id: "leste-inf", label: "Leste Inferior", price: 120.0, short: "Leste Inf" },
  { id: "leste-sup", label: "Leste Superior", price: 95.0, short: "Leste Sup" },
  { id: "oeste-inf", label: "Oeste Inferior", price: 180.0, short: "Oeste Inf" },
  { id: "oeste-sup", label: "Oeste Superior", price: 150.0, short: "Oeste Sup" },
  { id: "camarote", label: "Camarote (VIP)", price: 225.8, short: "Camarote" },
  { id: "visitante", label: "Setor Visitante", price: 89.9, short: "Visitante" },
];

// Cores por setor
const COLORS = {
  norte: "#047857",
  sul: "#0EA5A4",
  "leste-inf": "#4F46E5",
  "leste-sup": "#2563EB",
  "oeste-inf": "#C026D3",
  "oeste-sup": "#7C3AED",
  camarote: "#D97706",
  visitante: "#F43F5E",
};

// CPF simples
const validateCPF = (cpf) => {
  const onlyNums = cpf.replace(/\D/g, "");
  return onlyNums.length === 11;
};

// Arena Map
function ArenaMap({ onSelect, selected }) {
  return (
    <svg viewBox="0 0 800 500" className="w-full h-auto">
      <rect x="150" y="80" width="500" height="340" rx="20" fill="#0F9D58" />
      {/* Norte */}
      <g onClick={() => onSelect("norte")} aria-label="Arquibancada Norte" style={{ cursor: "pointer" }}>
        <rect x="220" y="30" width="360" height="50" rx="12" fill={COLORS.norte} opacity={selected === "norte" ? 1 : 0.95} />
        <text x="400" y="60" textAnchor="middle" fontSize="14" fill="#fff">Arquibancada Norte</text>
      </g>
      {/* Sul */}
      <g onClick={() => onSelect("sul")} aria-label="Arquibancada Sul" style={{ cursor: "pointer" }}>
        <rect x="220" y="420" width="360" height="50" rx="12" fill={COLORS.sul} opacity={selected === "sul" ? 1 : 0.95} />
        <text x="400" y="450" textAnchor="middle" fontSize="14" fill="#fff">Arquibancada Sul</text>
      </g>
      {/* Leste */}
      <g onClick={() => onSelect("leste-sup")} aria-label="Leste Superior" style={{ cursor: "pointer" }}>
        <rect x="670" y="120" width="90" height="110" rx="12" fill={COLORS["leste-sup"]} opacity={selected === "leste-sup" ? 1 : 0.95} />
        <text x="715" y="170" textAnchor="middle" fontSize="12" fill="#fff">Leste Sup</text>
      </g>
      <g onClick={() => onSelect("leste-inf")} aria-label="Leste Inferior" style={{ cursor: "pointer" }}>
        <rect x="670" y="260" width="90" height="120" rx="12" fill={COLORS["leste-inf"]} opacity={selected === "leste-inf" ? 1 : 0.95} />
        <text x="715" y="320" textAnchor="middle" fontSize="12" fill="#fff">Leste Inf</text>
      </g>
      {/* Oeste */}
      <g onClick={() => onSelect("oeste-sup")} aria-label="Oeste Superior" style={{ cursor: "pointer" }}>
        <rect x="40" y="120" width="90" height="110" rx="12" fill={COLORS["oeste-sup"]} opacity={selected === "oeste-sup" ? 1 : 0.95} />
        <text x="85" y="170" textAnchor="middle" fontSize="12" fill="#fff">Oeste Sup</text>
      </g>
      <g onClick={() => onSelect("oeste-inf")} aria-label="Oeste Inferior" style={{ cursor: "pointer" }}>
        <rect x="40" y="260" width="90" height="120" rx="12" fill={COLORS["oeste-inf"]} opacity={selected === "oeste-inf" ? 1 : 0.95} />
        <text x="85" y="320" textAnchor="middle" fontSize="12" fill="#fff">Oeste Inf</text>
      </g>
      {/* Camarote */}
      <g onClick={() => onSelect("camarote")} aria-label="Camarote" style={{ cursor: "pointer" }}>
        <rect x="320" y="10" width="160" height="18" rx="8" fill={COLORS.camarote} opacity={selected === "camarote" ? 1 : 0.95} />
        <text x="400" y="23" textAnchor="middle" fontSize="11" fill="#fff">Camarote</text>
      </g>
      {/* Visitante */}
      <g onClick={() => onSelect("visitante")} aria-label="Visitante" style={{ cursor: "pointer" }}>
        <rect x="520" y="420" width="120" height="50" rx="12" fill={COLORS.visitante} opacity={selected === "visitante" ? 1 : 0.95} />
        <text x="580" y="450" textAnchor="middle" fontSize="12" fill="#fff">Visitante</text>
      </g>
    </svg>
  );
}

function Cart({ cart, setCart, total, setShowPayment }) {
  const updateQty = (id, delta) => {
    setCart((prev) =>
      prev.map((item) =>
        item.id === id ? { ...item, qty: Math.max(1, item.qty + delta) } : item
      )
    );
  };

  return (
    <div className="rounded-2xl border p-6 bg-white shadow-sm">
      <div className="flex items-center justify-between">
        <div className="font-semibold">Carrinho</div>
        <div className="text-sm text-neutral-600">{cart.length} item(s)</div>
      </div>
      <div className="mt-4">
        {cart.length === 0 ? (
          <div className="text-sm text-neutral-600">Seu carrinho está vazio.</div>
        ) : (
          cart.map((it) => (
            <div key={it.id} className="flex items-center justify-between py-2 border-b last:border-b-0">
              <div>
                <div className="font-medium">{it.label}</div>
                <div className="flex gap-2 items-center text-xs text-neutral-600">
                  <button onClick={() => updateQty(it.id, -1)} className="px-2 border rounded">-</button>
                  <span>{it.qty}</span>
                  <button onClick={() => updateQty(it.id, 1)} className="px-2 border rounded">+</button>
                </div>
              </div>
              <div className="text-sm font-semibold">{brl(it.price * it.qty)}</div>
            </div>
          ))
        )}
      </div>
      <div className="mt-4 border-t pt-4 flex items-center justify-between">
        <div className="font-semibold">Total</div>
        <div className="font-semibold">{brl(total)}</div>
      </div>
      <div className="mt-4">
        <button
          disabled={cart.length === 0}
          onClick={() => setShowPayment(true)}
          className={`w-full rounded-2xl px-4 py-2 ${
            cart.length === 0 ? "bg-neutral-200 text-neutral-500" : "bg-black text-white"
          }`}
        >
          Ir para pagamento
        </button>
      </div>
    </div>
  );
}

function CheckoutForm({ buyer, setBuyer, handleCheckout, total, showPayment, setShowPayment }) {
  return (
    <div className={`rounded-2xl border p-6 bg-white shadow-sm ${!showPayment ? "opacity-60" : ""}`}>
      <h4 className="font-semibold">Pagamento e dados do ingresso</h4>
      {showPayment ? (
        <form onSubmit={handleCheckout} className="mt-4 space-y-3">
          <div>
            <label className="text-xs">Nome completo</label>
            <input value={buyer.fullName} onChange={(e) => setBuyer({ ...buyer, fullName: e.target.value })} required className="mt-1 w-full rounded-lg border px-3 py-2" />
          </div>
          <div>
            <label className="text-xs">Data de nascimento</label>
            <input type="date" value={buyer.birth} onChange={(e) => setBuyer({ ...buyer, birth: e.target.value })} required className="mt-1 w-full rounded-lg border px-3 py-2" />
          </div>
          <div>
            <label className="text-xs">CPF</label>
            <input value={buyer.cpf} onChange={(e) => setBuyer({ ...buyer, cpf: e.target.value })} required placeholder="000.000.000-00" className="mt-1 w-full rounded-lg border px-3 py-2" />
          </div>
          <div>
            <label className="text-xs">Nome no ingresso</label>
            <input value={buyer.ticketName} onChange={(e) => setBuyer({ ...buyer, ticketName: e.target.value })} required placeholder="Nome que aparecerá no ingresso" className="mt-1 w-full rounded-lg border px-3 py-2" />
          </div>
          <div className="pt-3 border-t flex items-center justify-between">
            <div className="text-sm text-neutral-600">Forma de pagamento (simulação)</div>
            <div className="text-sm font-semibold">Total {brl(total)}</div>
          </div>
          <div className="grid grid-cols-2 gap-2">
            <button type="submit" className="mt-3 rounded-2xl bg-black text-white px-4 py-2">Finalizar compra</button>
            <button type="button" onClick={() => setShowPayment(false)} className="mt-3 rounded-2xl border px-4 py-2">Voltar</button>
          </div>
        </form>
      ) : (
        <div className="text-sm text-neutral-600 mt-3">Adicione um setor ao carrinho para finalizar.</div>
      )}
    </div>
  );
}

export default function TicketSalesNeo() {
  const [selected, setSelected] = useState(null);
  const [cart, setCart] = useState([]);
  const [showPayment, setShowPayment] = useState(false);
  const [buyer, setBuyer] = useState({ fullName: "", birth: "", cpf: "", ticketName: "" });
  const [orderId, setOrderId] = useState(null);

  const selectedSector = useMemo(() => SECTORS.find((s) => s.id === selected), [selected]);
  const total = cart.reduce((acc, it) => acc + it.price * it.qty, 0);

  useEffect(() => {
    if (orderId) window.scrollTo({ top: 0, behavior: "smooth" });
  }, [orderId]);

  const addToCart = (sectorId) => {
    const sector = SECTORS.find((s) => s.id === sectorId);
    if (!sector) return;
    setCart([{ ...sector, qty: 1 }]);
    setShowPayment(true);
  };

  const handleCheckout = (e) => {
    e.preventDefault();
    if (!buyer.fullName || !buyer.birth || !buyer.cpf || !buyer.ticketName) return alert("Preencha todos os campos.");
    if (!validateCPF(buyer.cpf)) return alert("CPF inválido");
    const id = "ING" + Math.random().toString(36).slice(2, 9).toUpperCase();
    setOrderId(id);
    setCart([]);
    setSelected(null);
    setShowPayment(false);
    alert(`Compra simulada! Pedido ${id} — Enviamos o e-ticket para o e-mail cadastrado.`);
  };

  return (
    <div className="min-h-screen bg-gradient-to-b from-neutral-50 to-white text-neutral-900 font-sans">
      <header className="bg-black text-white py-4 shadow-md">
        <div className="max-w-6xl mx-auto px-6 flex items-center justify-between">
          <div className="flex items-center gap-4">
            <div className="w-10 h-10 rounded-full bg-white/10 grid place-items-center font-bold">CT</div>
            <div>
              <div className="text-sm opacity-90">Ingresso Oficial</div>
              <div className="text-lg font-extrabold tracking-tight">{MATCH.home} x {MATCH.away}</div>
            </div>
          </div>
          <div className="text-sm opacity-70">{new Date(MATCH.dateISO).toLocaleDateString('pt-BR')} — {MATCH.stadium}</div>
        </div>
      </header>

      <main className="max-w-6xl mx-auto px-6 py-10 grid grid-cols-1 lg:grid-cols-[1fr,1.6fr,1fr] gap-8">
        {/* Coluna esquerda */}
        <motion.aside initial={{ opacity: 0, y: 10 }} animate={{ opacity: 1, y: 0 }} transition={{ delay: 0.05 }} className="space-y-6">
          <div className="rounded-2xl border p-6 bg-white shadow-sm">
            <h3 className="font-semibold text-lg">Seu nome</h3>
            <input value={buyer.fullName} onChange={(e) => setBuyer({ ...buyer, fullName: e.target.value })} placeholder="Nome completo" className="mt-3 w-full rounded-lg border px-4 py-2" />
            <p className="mt-3 text-sm text-neutral-600">Preencha seu nome — usado para contato.</p>
          </div>
          <div className="rounded-2xl border p-6 bg-white shadow-sm space-y-3">
            <h4 className="font-semibold">Políticas do estádio</h4>
            <ul className="text-sm text-neutral-700 space-y-2">
              <li>• Proibida entrada com bebidas em vidro.</li>
              <li>• Meia-entrada sujeita à comprovação.</li>
              <li>• Organização pode realizar revista.</li>
              <li>• Ingressos nominais — documento com foto.</li>
            </ul>
          </div>
          <footer className="text-xs text-neutral-600 text-center mt-6">Compra segura desde 2021</footer>
        </motion.aside>

        {/* Mapa */}
        <motion.section initial={{ opacity: 0, y: 8 }} animate={{ opacity: 1, y: 0 }} transition={{ delay: 0.1 }} className="rounded-3xl border bg-white p-6 shadow-sm">
          <div className="flex items-center justify-between mb-4">
           
