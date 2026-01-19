<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TBS PRO - Enterprise Edition</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap');
        
        :root {
            --primary: #059669;
            --primary-dark: #064e3b;
            --accent: #10b981;
        }

        body { 
            font-family: 'Plus Jakarta Sans', sans-serif; 
            background-color: #f1f5f9;
        }

        .tab-content {
            display: none;
            animation: fadeIn 0.4s cubic-bezier(0.4, 0, 0.2, 1);
        }

        .tab-content.active {
            display: block;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(8px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .sidebar-link {
            transition: all 0.2s ease;
        }

        .sidebar-link.active {
            background: linear-gradient(to right, rgba(16, 185, 129, 0.15), transparent);
            color: #10b981;
            border-left: 4px solid #10b981;
        }

        .pro-card {
            background: #ffffff;
            border: 1px solid #e2e8f0;
            box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.05);
            border-radius: 1.25rem;
        }

        .input-pro {
            background-color: #f8fafc;
            border: 1.5px solid #e2e8f0;
            transition: all 0.2s;
        }

        .input-pro:focus {
            background-color: #ffffff;
            border-color: var(--primary);
            outline: none;
            ring: 3px rgba(5, 150, 105, 0.1);
        }

        .btn-primary {
            background: linear-gradient(135deg, #059669 0%, #047857 100%);
        }

        /* Custom Scrollbar */
        ::-webkit-scrollbar { width: 6px; }
        ::-webkit-scrollbar-track { background: #f1f1f1; }
        ::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 10px; }
    </style>
</head>
<body class="text-slate-800">

    <div class="flex flex-col lg:flex-row min-h-screen">
        <!-- Sidebar -->
        <aside class="w-full lg:w-72 bg-slate-900 text-slate-300 flex flex-col sticky top-0 h-screen z-50">
            <div class="p-8">
                <div class="flex items-center gap-3">
                    <div class="w-10 h-10 bg-emerald-500 rounded-xl flex items-center justify-center shadow-lg">
                        <i data-lucide="leaf" class="text-white w-6 h-6"></i>
                    </div>
                    <div>
                        <h1 class="text-xl font-extrabold tracking-tight text-white">TBS PRO</h1>
                        <p class="text-[10px] font-bold text-emerald-500 tracking-[0.2em] uppercase">Enterprise</p>
                    </div>
                </div>
            </div>
            
            <nav class="flex-1 px-4 space-y-1.5 mt-4 overflow-y-auto">
                <button onclick="showTab('dashboard')" id="btn-dashboard" class="sidebar-link w-full flex items-center gap-3 px-4 py-3 rounded-xl hover:text-white hover:bg-white/5 font-semibold text-sm">
                    <i data-lucide="layout-grid" class="w-5 h-5"></i> Dashboard
                </button>
                <button onclick="showTab('pembelian')" id="btn-pembelian" class="sidebar-link w-full flex items-center gap-3 px-4 py-3 rounded-xl hover:text-white hover:bg-white/5 font-semibold text-sm">
                    <i data-lucide="shopping-bag" class="w-5 h-5"></i> Pembelian (Ram)
                </button>
                <button onclick="showTab('pengiriman')" id="btn-pengiriman" class="sidebar-link w-full flex items-center gap-3 px-4 py-3 rounded-xl hover:text-white hover:bg-white/5 font-semibold text-sm">
                    <i data-lucide="truck" class="w-5 h-5"></i> Logistik (DO)
                </button>
                <button onclick="showTab('penjualan')" id="btn-penjualan" class="sidebar-link w-full flex items-center gap-3 px-4 py-3 rounded-xl hover:text-white hover:bg-white/5 font-semibold text-sm">
                    <i data-lucide="factory" class="w-5 h-5"></i> Penjualan (PKS)
                </button>
                <button onclick="showTab('lossis')" id="btn-lossis" class="sidebar-link w-full flex items-center gap-3 px-4 py-3 rounded-xl hover:text-white hover:bg-white/5 font-semibold text-sm">
                    <i data-lucide="scale" class="w-5 h-5"></i> Edit Stok & Lossis
                </button>
                <div class="h-[1px] bg-slate-800 my-4 mx-4"></div>
                <button onclick="showTab('keuangan')" id="btn-keuangan" class="sidebar-link w-full flex items-center gap-3 px-4 py-3 rounded-xl hover:text-white hover:bg-white/5 font-semibold text-sm">
                    <i data-lucide="landmark" class="w-5 h-5"></i> Manajemen Kas
                </button>
                <button onclick="showTab('operasional')" id="btn-operasional" class="sidebar-link w-full flex items-center gap-3 px-4 py-3 rounded-xl hover:text-white hover:bg-white/5 font-semibold text-sm">
                    <i data-lucide="receipt" class="w-5 h-5"></i> Operasional
                </button>
            </nav>

            <div class="p-6">
                <button onclick="exportToExcel()" class="btn-primary w-full flex items-center justify-center gap-2 py-3.5 text-xs font-bold text-white rounded-xl transition-all active:scale-95">
                    <i data-lucide="download-cloud" class="w-4 h-4"></i> EXPORT EXCEL
                </button>
            </div>
        </aside>

        <main class="flex-1 flex flex-col h-screen overflow-hidden">
            <header class="h-20 bg-white border-b flex items-center justify-between px-10 flex-shrink-0">
                <div>
                    <h2 id="current-title" class="text-xl font-bold text-slate-900 tracking-tight">Overview</h2>
                    <p class="text-xs text-slate-500 font-medium" id="current-date"></p>
                </div>
                <div class="flex items-center gap-4">
                    <div class="text-right hidden sm:block">
                        <p class="text-xs font-bold text-slate-900">Administrator</p>
                        <p class="text-[10px] text-emerald-600 font-bold uppercase">TBS-PRO-01</p>
                    </div>
                    <div class="w-10 h-10 bg-slate-100 rounded-full flex items-center justify-center text-slate-600 font-bold border border-slate-200">AD</div>
                </div>
            </header>

            <div class="p-10 overflow-y-auto flex-1 bg-slate-50">
                
                <!-- Dashboard -->
                <section id="content-dashboard" class="tab-content active">
                    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 mb-10">
                        <div class="pro-card p-6 border-b-4 border-b-emerald-500">
                            <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-1">Kas Tersedia</p>
                            <h3 id="dash-modal" class="text-2xl font-extrabold text-slate-900">Rp 0</h3>
                        </div>
                        <div class="pro-card p-6 border-b-4 border-b-blue-500">
                            <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-1">Stok Ram (Netto Riil)</p>
                            <h3 id="dash-stok-akhir" class="text-2xl font-extrabold text-slate-900">0 Kg</h3>
                        </div>
                        <div class="pro-card p-6 border-b-4 border-b-amber-500">
                            <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-1">Total Kirim PKS</p>
                            <h3 id="dash-do-total" class="text-2xl font-extrabold text-slate-900">0 Kg</h3>
                        </div>
                        <div class="pro-card p-6 border-b-4 border-b-indigo-600">
                            <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-1">Laba Bersih</p>
                            <h3 id="dash-profit" class="text-2xl font-extrabold text-indigo-600">Rp 0</h3>
                        </div>
                    </div>

                    <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
                        <div class="lg:col-span-2 pro-card p-8">
                            <h4 class="font-bold text-lg mb-8">Neraca Saldo Presisi</h4>
                            <div class="space-y-4">
                                <div class="flex justify-between p-4 bg-slate-50 rounded-xl"><span class="text-slate-500 font-medium">Penjualan PKS (Netto Akhir)</span><span id="lr-penjualan" class="font-bold text-slate-900 text-lg">Rp 0</span></div>
                                <div class="flex justify-between p-4 bg-slate-50 rounded-xl"><span class="text-slate-500 font-medium">Pembelian Ram (Netto Akhir)</span><span id="lr-hpp" class="font-bold text-red-500">Rp 0</span></div>
                                <div class="flex justify-between p-4 bg-slate-50 rounded-xl"><span class="text-slate-500 font-medium">Biaya Operasional</span><span id="lr-ops" class="font-bold text-orange-500">Rp 0</span></div>
                                <div class="pt-6 border-t flex justify-between items-center">
                                    <span class="text-lg font-bold text-slate-900">Profit / Loss (Riil)</span>
                                    <span id="lr-bersih" class="text-3xl font-black text-emerald-600">Rp 0</span>
                                </div>
                            </div>
                        </div>
                        <div class="pro-card p-8 bg-slate-900 text-white border-none flex flex-col">
                            <h4 class="text-emerald-400 font-bold text-xs uppercase tracking-widest mb-6">Analisa Susut & Lossis</h4>
                            <div class="space-y-6 flex-1">
                                <div>
                                    <p class="text-[10px] opacity-50 uppercase font-bold mb-1">Akumulasi Susut (Kirim vs PKS)</p>
                                    <p id="total-susut" class="text-2xl font-bold text-red-400">0 Kg</p>
                                </div>
                                <div>
                                    <p class="text-[10px] opacity-50 uppercase font-bold mb-1">Total Koreksi Lossis/Stok</p>
                                    <p id="total-lossis" class="text-2xl font-bold text-blue-400">0 Kg</p>
                                </div>
                            </div>
                            <div class="mt-8 p-4 bg-white/5 rounded-xl border border-white/10 italic text-[10px] leading-relaxed opacity-60">
                                Stok Ram dihitung berdasarkan Berat Bersih Pembelian dikurangi Pengiriman dan disesuaikan dengan Manual Lossis.
                            </div>
                        </div>
                    </div>
                </section>

                <!-- Pembelian -->
                <section id="content-pembelian" class="tab-content">
                    <div class="grid grid-cols-1 xl:grid-cols-4 gap-8">
                        <div class="pro-card p-8 h-fit">
                            <h3 class="font-bold text-slate-900 mb-6 flex items-center gap-2 text-lg"><i data-lucide="plus" class="w-5 h-5 text-emerald-600"></i> Entri Pembelian</h3>
                            <form id="form-pembelian" onsubmit="handleForm(event, 'pembelian')" class="space-y-4">
                                <div><label class="text-[10px] font-bold uppercase text-slate-400 mb-1 block">Tanggal</label><input type="date" id="beli-tgl" class="w-full input-pro p-3.5 rounded-xl text-sm font-medium" required></div>
                                <div><label class="text-[10px] font-bold uppercase text-slate-400 mb-1 block">Pemasok</label><input type="text" id="beli-pemasok" class="w-full input-pro p-3.5 rounded-xl text-sm" placeholder="Nama Petani" required></div>
                                <div class="grid grid-cols-2 gap-3">
                                    <div><label class="text-[10px] font-bold uppercase text-slate-400 mb-1 block">Brutto</label><input type="number" id="beli-bruto" class="w-full input-pro p-3.5 rounded-xl text-sm" oninput="calcPurchase()" required></div>
                                    <div><label class="text-[10px] font-bold uppercase text-slate-400 mb-1 block">Tara</label><input type="number" id="beli-tara" class="w-full input-pro p-3.5 rounded-xl text-sm" oninput="calcPurchase()" required></div>
                                </div>
                                <div class="p-4 bg-slate-50 rounded-xl border border-dashed border-slate-300">
                                    <div class="flex flex-col gap-1 mb-2">
                                        <label class="text-[10px] font-bold uppercase text-slate-400">Potongan (%)</label>
                                        <input type="number" id="beli-pot-pct" step="0.1" value="0" class="w-full input-pro p-2 rounded-lg text-xs font-bold text-red-600" oninput="calcPurchase()">
                                    </div>
                                    <div class="pt-2 border-t border-slate-200 flex justify-between items-center"><span class="text-[10px] font-bold text-emerald-600 uppercase">Netto Akhir</span><span id="beli-netto-label" class="text-lg font-black text-emerald-700">0 Kg</span></div>
                                </div>
                                <div><label class="text-[10px] font-bold uppercase text-slate-400 mb-1 block">Harga Beli (Rp/Kg)</label><input type="number" id="beli-harga" class="w-full input-pro p-3.5 rounded-xl text-sm font-bold text-slate-900" oninput="calcPurchase()" required></div>
                                <div class="bg-emerald-600 p-4 rounded-xl text-white shadow-lg shadow-emerald-500/20">
                                    <p class="text-[10px] font-bold opacity-70 uppercase mb-1">Total Bayar</p>
                                    <h4 id="beli-total-label" class="text-xl font-black">Rp 0</h4>
                                </div>
                                <button type="submit" class="btn-primary w-full text-white font-bold py-4 rounded-xl shadow-lg active:scale-95 transition-all text-sm">SIMPAN PEMBELIAN</button>
                            </form>
                        </div>
                        <div class="xl:col-span-3 pro-card overflow-hidden">
                            <table class="w-full text-left">
                                <thead class="bg-slate-50 text-[10px] font-bold text-slate-400 uppercase tracking-widest border-b">
                                    <tr><th class="p-5">Waktu</th><th class="p-5">Pemasok</th><th class="p-5">Netto Akhir</th><th class="p-5">Harga</th><th class="p-5">Total</th><th class="p-5 text-right">Aksi</th></tr>
                                </thead>
                                <tbody id="list-pembelian" class="divide-y divide-slate-100"></tbody>
                            </table>
                        </div>
                    </div>
                </section>

                <!-- Lossis & Edit Stok -->
                <section id="content-lossis" class="tab-content">
                    <div class="grid grid-cols-1 xl:grid-cols-4 gap-8">
                        <div class="pro-card p-8 h-fit">
                            <h3 class="font-bold text-slate-900 mb-6 flex items-center gap-2 text-lg"><i data-lucide="scale" class="text-blue-600"></i> Penyesuaian Stok</h3>
                            <form onsubmit="handleForm(event, 'lossis')" class="space-y-4">
                                <div><label class="text-[10px] font-bold uppercase text-slate-400 mb-1 block">Tanggal Koreksi</label><input type="date" id="lossis-tgl" class="w-full input-pro p-3.5 rounded-xl text-sm" required></div>
                                <div>
                                    <label class="text-[10px] font-bold uppercase text-slate-400 mb-1 block">Jenis Penyesuaian</label>
                                    <select id="lossis-tipe" class="w-full input-pro p-3.5 rounded-xl text-sm font-medium">
                                        <option value="kurang">Pengurangan (Lossis/Susut Restan)</option>
                                        <option value="tambah">Penambahan (Koreksi Plus)</option>
                                    </select>
                                </div>
                                <div><label class="text-[10px] font-bold uppercase text-slate-400 mb-1 block">Jumlah Berat (Kg)</label><input type="number" id="lossis-kg" class="w-full input-pro p-3.5 rounded-xl text-sm font-bold" placeholder="0" required></div>
                                <div><label class="text-[10px] font-bold uppercase text-slate-400 mb-1 block">Keterangan / Alasan</label><textarea id="lossis-ket" class="w-full input-pro p-3.5 rounded-xl text-sm" rows="3" placeholder="Contoh: Susut timbangan atau tumpukan kehujanan"></textarea></div>
                                <button type="submit" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-4 rounded-xl shadow-lg transition-all active:scale-95 text-sm uppercase">Update Stok</button>
                            </form>
                        </div>
                        <div class="xl:col-span-3 pro-card overflow-hidden">
                            <div class="p-6 bg-blue-50 border-b flex justify-between items-center">
                                <div>
                                    <h4 class="font-bold text-blue-900">Riwayat Penyesuaian Stok</h4>
                                    <p class="text-xs text-blue-700">Daftar manual lossis yang mempengaruhi stok gudang</p>
                                </div>
                                <div class="text-right">
                                    <p class="text-[10px] font-bold text-blue-400 uppercase">Total Koreksi</p>
                                    <p id="total-lossis-list" class="text-xl font-black text-blue-900">0 Kg</p>
                                </div>
                            </div>
                            <table class="w-full text-left">
                                <thead class="bg-slate-50 text-[10px] font-bold text-slate-400 uppercase tracking-widest border-b">
                                    <tr><th class="p-5">Tanggal</th><th class="p-5">Keterangan</th><th class="p-5">Tipe</th><th class="p-5">Berat</th><th class="p-5 text-right">Aksi</th></tr>
                                </thead>
                                <tbody id="list-lossis" class="divide-y divide-slate-100"></tbody>
                            </table>
                        </div>
                    </div>
                </section>

                <!-- Lainnya (DO, PKS, etc - tetap ada sesuai kode sebelumnya) -->
                <section id="content-pengiriman" class="tab-content">
                    <div class="grid grid-cols-1 xl:grid-cols-4 gap-8">
                        <div class="pro-card p-8 h-fit">
                            <h3 class="font-bold text-slate-900 mb-6 flex items-center gap-2 text-lg"><i data-lucide="truck" class="text-blue-600"></i> Pengiriman DO</h3>
                            <form onsubmit="handleForm(event, 'pengiriman')" class="space-y-4">
                                <div><label class="text-[10px] font-bold uppercase text-slate-400 mb-1 block">Tanggal Kirim</label><input type="date" id="kirim-tgl" class="w-full input-pro p-3.5 rounded-xl text-sm" required></div>
                                <div><label class="text-[10px] font-bold uppercase text-slate-400 mb-1 block">Tujuan PKS</label><input type="text" id="kirim-pks" class="w-full input-pro p-3.5 rounded-xl text-sm" placeholder="Nama PKS" required></div>
                                <div><label class="text-[10px] font-bold uppercase text-slate-400 mb-1 block">Brutto Mobil</label><input type="number" id="kirim-bruto" class="w-full input-pro p-3.5 rounded-xl text-sm" oninput="calcNetto('kirim')" required></div>
                                <div><label class="text-[10px] font-bold uppercase text-slate-400 mb-1 block">Tara Mobil</label><input type="number" id="kirim-tara" class="w-full input-pro p-3.5 rounded-xl text-sm" oninput="calcNetto('kirim')" required></div>
                                <div class="p-4 bg-blue-50 rounded-xl border border-blue-100 flex justify-between items-center"><span class="text-xs font-bold text-blue-400 uppercase">Netto Kirim</span><span id="kirim-netto-label" class="font-black text-blue-700">0 Kg</span></div>
                                <button type="submit" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-4 rounded-xl shadow-lg transition-all active:scale-95 text-sm uppercase">Kirim Barang</button>
                            </form>
                        </div>
                        <div class="xl:col-span-3 pro-card overflow-hidden">
                            <table class="w-full text-left">
                                <thead class="bg-slate-50 text-[10px] font-bold text-slate-400 uppercase tracking-widest border-b">
                                    <tr><th class="p-5">Tanggal</th><th class="p-5">PKS</th><th class="p-5">Netto Kirim</th><th class="p-5">Status</th><th class="p-5 text-right">Aksi</th></tr>
                                </thead>
                                <tbody id="list-pengiriman" class="divide-y divide-slate-100"></tbody>
                            </table>
                        </div>
                    </div>
                </section>

                <section id="content-penjualan" class="tab-content">
                    <div class="grid grid-cols-1 xl:grid-cols-4 gap-8">
                        <div class="pro-card p-8 h-fit">
                            <h3 class="font-bold text-slate-900 mb-6 flex items-center gap-2 text-lg"><i data-lucide="factory" class="text-indigo-600"></i> Hasil PKS</h3>
                            <form onsubmit="handleForm(event, 'penjualan')" class="space-y-4">
                                <div>
                                    <label class="text-[10px] font-bold uppercase text-slate-400 mb-1 block">Pilih DO</label>
                                    <select id="jual-id-pengiriman" class="w-full input-pro p-3.5 rounded-xl text-sm" onchange="fillJualInfo()" required>
                                        <option value="">-- Pilih DO --</option>
                                    </select>
                                </div>
                                <div><label class="text-[10px] font-bold uppercase text-slate-400 mb-1 block">Netto Timbangan PKS</label><input type="number" id="jual-netto-bruto" class="w-full input-pro p-3.5 rounded-xl text-sm" oninput="calcJual()" required></div>
                                <div><label class="text-[10px] font-bold uppercase text-slate-400 mb-1 block">Pot PKS (%)</label><input type="number" id="jual-pot-pct" step="0.1" value="0" class="w-full input-pro p-3.5 rounded-xl text-sm" oninput="calcJual()"></div>
                                <div><label class="text-[10px] font-bold uppercase text-slate-400 mb-1 block">Harga PKS</label><input type="number" id="jual-harga" class="w-full input-pro p-3.5 rounded-xl text-sm" oninput="calcJual()" required></div>
                                <div class="bg-indigo-600 p-4 rounded-xl text-white">
                                    <p class="text-[10px] opacity-70 uppercase font-bold mb-1">Total Cair</p>
                                    <h4 id="jual-total-label" class="text-xl font-black">Rp 0</h4>
                                </div>
                                <button type="submit" class="w-full bg-indigo-700 hover:bg-indigo-800 text-white font-bold py-4 rounded-xl shadow-lg transition-all active:scale-95 text-sm uppercase">Simpan Penjualan</button>
                            </form>
                        </div>
                        <div class="xl:col-span-3 pro-card overflow-hidden">
                            <table class="w-full text-left">
                                <thead class="bg-slate-50 text-[10px] font-bold text-slate-400 uppercase tracking-widest border-b">
                                    <tr><th class="p-5">Tanggal</th><th class="p-5">Netto PKS</th><th class="p-5">Harga</th><th class="p-5">Total</th><th class="p-5 text-right">Aksi</th></tr>
                                </thead>
                                <tbody id="list-penjualan" class="divide-y divide-slate-100"></tbody>
                            </table>
                        </div>
                    </div>
                </section>

                <section id="content-keuangan" class="tab-content">
                    <div class="grid grid-cols-1 xl:grid-cols-4 gap-8">
                        <div class="pro-card p-8 h-fit">
                            <h3 class="font-bold text-slate-900 mb-6 flex items-center gap-2 text-lg"><i data-lucide="plus" class="text-emerald-600"></i> Modal</h3>
                            <form onsubmit="handleForm(event, 'modal')" class="space-y-4">
                                <div><label class="text-[10px] font-bold uppercase text-slate-400 mb-1 block">Tanggal</label><input type="date" id="modal-tgl" class="w-full input-pro p-3.5 rounded-xl text-sm" required></div>
                                <div><label class="text-[10px] font-bold uppercase text-slate-400 mb-1 block">Keterangan</label><input type="text" id="modal-ket" class="w-full input-pro p-3.5 rounded-xl text-sm" placeholder="Modal awal" required></div>
                                <div><label class="text-[10px] font-bold uppercase text-slate-400 mb-1 block">Jumlah (Rp)</label><input type="number" id="modal-amount" class="w-full input-pro p-3.5 rounded-xl text-sm" required></div>
                                <button type="submit" class="w-full bg-emerald-600 hover:bg-emerald-700 text-white font-bold py-4 rounded-xl shadow-lg active:scale-95 transition-all text-sm uppercase">Simpan Modal</button>
                            </form>
                        </div>
                        <div class="xl:col-span-3 pro-card overflow-hidden">
                            <table class="w-full text-left">
                                <thead class="bg-slate-50 text-[10px] font-bold text-slate-400 uppercase tracking-widest border-b">
                                    <tr><th class="p-5">Tanggal</th><th class="p-5">Keterangan</th><th class="p-5 text-right">Jumlah</th><th class="p-5 text-right">Aksi</th></tr>
                                </thead>
                                <tbody id="list-modal" class="divide-y divide-slate-100"></tbody>
                            </table>
                        </div>
                    </div>
                </section>

                <section id="content-operasional" class="tab-content">
                    <div class="grid grid-cols-1 xl:grid-cols-4 gap-8">
                        <div class="pro-card p-8 h-fit">
                            <h3 class="font-bold text-slate-900 mb-6 flex items-center gap-2 text-lg"><i data-lucide="receipt" class="text-orange-600"></i> Ops</h3>
                            <form onsubmit="handleForm(event, 'operasional')" class="space-y-4">
                                <div><label class="text-[10px] font-bold uppercase text-slate-400 mb-1 block">Tanggal</label><input type="date" id="ops-tgl" class="w-full input-pro p-3.5 rounded-xl text-sm" required></div>
                                <div><label class="text-[10px] font-bold uppercase text-slate-400 mb-1 block">Jenis Biaya</label><input type="text" id="ops-ket" class="w-full input-pro p-3.5 rounded-xl text-sm" required></div>
                                <div><label class="text-[10px] font-bold uppercase text-slate-400 mb-1 block">Jumlah (Rp)</label><input type="number" id="ops-amount" class="w-full input-pro p-3.5 rounded-xl text-sm" required></div>
                                <button type="submit" class="w-full bg-orange-600 hover:bg-orange-700 text-white font-bold py-4 rounded-xl shadow-lg active:scale-95 transition-all text-sm uppercase">Simpan Biaya</button>
                            </form>
                        </div>
                        <div class="xl:col-span-3 pro-card overflow-hidden">
                            <table class="w-full text-left">
                                <thead class="bg-slate-50 text-[10px] font-bold text-slate-400 uppercase tracking-widest border-b">
                                    <tr><th class="p-5">Tanggal</th><th class="p-5">Keterangan</th><th class="p-5 text-right">Biaya</th><th class="p-5 text-right">Aksi</th></tr>
                                </thead>
                                <tbody id="list-operasional" class="divide-y divide-slate-100"></tbody>
                            </table>
                        </div>
                    </div>
                </section>

            </div>
        </main>
    </div>

    <!-- Notification -->
    <div id="toast" class="fixed bottom-10 left-1/2 -translate-x-1/2 opacity-0 translate-y-10 transition-all duration-500 z-[100]">
        <div class="bg-slate-900 text-white px-8 py-4 rounded-2xl shadow-2xl border border-white/10 flex items-center gap-4">
            <div class="w-2 h-2 rounded-full bg-emerald-500 animate-pulse"></div>
            <p id="toast-msg" class="text-sm font-bold tracking-tight"></p>
        </div>
    </div>

    <script>
        // Database Init
        let db = JSON.parse(localStorage.getItem('tbs_pro_v3_lossis')) || {
            pembelian: [], 
            pengiriman: [], 
            penjualan: [], 
            operasional: [], 
            modal: [],
            lossis: [] // Fitur baru
        };

        const menus = {
            dashboard: "Dashboard Overview",
            pembelian: "Pembelian TBS",
            pengiriman: "Logistik (DO)",
            penjualan: "Hasil PKS",
            lossis: "Manajemen Lossis & Stok",
            keuangan: "Manajemen Kas",
            operasional: "Biaya Operasional"
        };

        window.onload = () => {
            lucide.createIcons();
            initDates();
            updateUI();
            showTab('dashboard');
        };

        function initDates() {
            const today = new Date().toISOString().split('T')[0];
            document.querySelectorAll('input[type="date"]').forEach(el => el.value = today);
            document.getElementById('current-date').innerText = new Intl.DateTimeFormat('id-ID', { dateStyle: 'full' }).format(new Date());
        }

        function showTab(tab) {
            document.querySelectorAll('.tab-content').forEach(c => c.classList.remove('active'));
            document.querySelectorAll('.sidebar-link').forEach(b => b.classList.remove('active'));
            document.getElementById('content-' + tab).classList.add('active');
            document.getElementById('btn-' + tab).classList.add('active');
            document.getElementById('current-title').innerText = menus[tab];
            
            if(tab === 'penjualan') populateDOSync();
            updateDashboard();
        }

        function formatRp(num) {
            return new Intl.NumberFormat('id-ID', { style: 'currency', currency: 'IDR', maximumFractionDigits: 0 }).format(num || 0);
        }

        function notify(msg) {
            const t = document.getElementById('toast');
            document.getElementById('toast-msg').innerText = msg;
            t.classList.remove('opacity-0', 'translate-y-10');
            setTimeout(() => t.classList.add('opacity-0', 'translate-y-10'), 3000);
        }

        // Logic Functions
        function calcPurchase() {
            const bruto = parseFloat(document.getElementById('beli-bruto').value) || 0;
            const tara = parseFloat(document.getElementById('beli-tara').value) || 0;
            const potPct = parseFloat(document.getElementById('beli-pot-pct').value) || 0;
            const harga = parseFloat(document.getElementById('beli-harga').value) || 0;
            const nettoAwal = Math.max(0, bruto - tara);
            const nettoFinal = nettoAwal - (nettoAwal * (potPct / 100));
            const total = Math.round(nettoFinal * harga);
            document.getElementById('beli-netto-label').innerText = Math.round(nettoFinal).toLocaleString() + ' Kg';
            document.getElementById('beli-total-label').innerText = formatRp(total);
            return { nettoFinal, total };
        }

        function calcNetto(prefix) {
            const bruto = parseFloat(document.getElementById(prefix + '-bruto').value) || 0;
            const tara = parseFloat(document.getElementById(prefix + '-tara').value) || 0;
            const netto = Math.max(0, bruto - tara);
            document.getElementById(prefix + '-netto-label').innerText = netto.toLocaleString() + ' Kg';
            return netto;
        }

        function fillJualInfo() {
            const id = document.getElementById('jual-id-pengiriman').value;
            const item = db.pengiriman.find(x => x.id == id);
            if(item) {
                document.getElementById('jual-netto-bruto').value = item.netto;
                calcJual();
            }
        }

        function calcJual() {
            const nettoInput = parseFloat(document.getElementById('jual-netto-bruto').value) || 0;
            const potPct = parseFloat(document.getElementById('jual-pot-pct').value) || 0;
            const harga = parseFloat(document.getElementById('jual-harga').value) || 0;
            const nettoFinal = nettoInput - (nettoInput * (potPct / 100));
            const total = Math.round(nettoFinal * harga);
            document.getElementById('jual-total-label').innerText = formatRp(total);
            return { nettoFinal, total };
        }

        function populateDOSync() {
            const select = document.getElementById('jual-id-pengiriman');
            const dataKirim = db.pengiriman.filter(x => x.status === 'Kirim');
            select.innerHTML = '<option value="">-- Pilih DO --</option>' + 
                dataKirim.map(x => `<option value="${x.id}">${x.pks} - ${x.tgl} (${x.netto}Kg)</option>`).join('');
        }

        function handleForm(e, type) {
            e.preventDefault();
            const data = { id: Date.now() };

            if(type === 'pembelian') {
                const res = calcPurchase();
                Object.assign(data, {
                    tgl: document.getElementById('beli-tgl').value,
                    pemasok: document.getElementById('beli-pemasok').value,
                    netto: res.nettoFinal,
                    harga: parseFloat(document.getElementById('beli-harga').value),
                    total: res.total
                });
                db.pembelian.push(data);
            } 
            else if(type === 'lossis') {
                const tipe = document.getElementById('lossis-tipe').value;
                const kg = parseFloat(document.getElementById('lossis-kg').value);
                Object.assign(data, {
                    tgl: document.getElementById('lossis-tgl').value,
                    tipe: tipe,
                    kg: kg,
                    netto: tipe === 'kurang' ? -kg : kg, // Nilai minus jika pengurangan
                    ket: document.getElementById('lossis-ket').value
                });
                db.lossis.push(data);
            }
            else if(type === 'pengiriman') {
                const netto = calcNetto('kirim');
                Object.assign(data, {
                    tgl: document.getElementById('kirim-tgl').value,
                    pks: document.getElementById('kirim-pks').value,
                    netto: netto,
                    status: 'Kirim'
                });
                db.pengiriman.push(data);
            }
            else if(type === 'penjualan') {
                const idKirim = document.getElementById('jual-id-pengiriman').value;
                const res = calcJual();
                Object.assign(data, {
                    idKirim: idKirim,
                    tgl: new Date().toISOString().split('T')[0],
                    netto: res.nettoFinal,
                    harga: parseFloat(document.getElementById('jual-harga').value),
                    total: res.total
                });
                db.penjualan.push(data);
                const kIdx = db.pengiriman.findIndex(x => x.id == idKirim);
                if(kIdx !== -1) db.pengiriman[kIdx].status = 'Selesai';
            }
            else if(type === 'modal') {
                Object.assign(data, {
                    tgl: document.getElementById('modal-tgl').value,
                    ket: document.getElementById('modal-ket').value,
                    amount: parseFloat(document.getElementById('modal-amount').value)
                });
                db.modal.push(data);
            }
            else if(type === 'operasional') {
                Object.assign(data, {
                    tgl: document.getElementById('ops-tgl').value,
                    ket: document.getElementById('ops-ket').value,
                    amount: parseFloat(document.getElementById('ops-amount').value)
                });
                db.operasional.push(data);
            }

            saveAndSync();
            e.target.reset();
            initDates();
            notify("Data berhasil disimpan");
        }

        function deleteRecord(type, id) {
            if(!confirm("Hapus data ini?")) return;
            if(type === 'penjualan') {
                const item = db.penjualan.find(x => x.id == id);
                if(item) {
                    const kIdx = db.pengiriman.findIndex(x => x.id == item.idKirim);
                    if(kIdx !== -1) db.pengiriman[kIdx].status = 'Kirim';
                }
            }
            db[type] = db[type].filter(x => x.id !== id);
            saveAndSync();
        }

        function saveAndSync() {
            localStorage.setItem('tbs_pro_v3_lossis', JSON.stringify(db));
            updateUI();
        }

        function updateDashboard() {
            const sum = (arr, key) => arr.reduce((a, b) => a + (b[key] || 0), 0);
            
            const totalModal = sum(db.modal, 'amount');
            const totalBeli = sum(db.pembelian, 'total');
            const totalJual = sum(db.penjualan, 'total');
            const totalOps = sum(db.operasional, 'amount');
            
            // Rumus Stok: Pembelian (Netto Akhir) - Pengiriman + Lossis (Manual Koreksi)
            const totalBeliKg = sum(db.pembelian, 'netto');
            const totalKirimKg = sum(db.pengiriman, 'netto');
            const totalLossisKg = sum(db.lossis, 'netto');
            const stokRamKg = totalBeliKg - totalKirimKg + totalLossisKg;

            let susutKg = 0;
            db.penjualan.forEach(j => {
                const d = db.pengiriman.find(p => p.id == j.idKirim);
                if(d) susutKg += (d.netto - j.netto);
            });

            document.getElementById('dash-modal').innerText = formatRp(totalModal - totalBeli + totalJual - totalOps);
            document.getElementById('dash-stok-akhir').innerText = Math.round(stokRamKg).toLocaleString() + ' Kg';
            document.getElementById('dash-do-total').innerText = totalKirimKg.toLocaleString() + ' Kg';
            document.getElementById('dash-profit').innerText = formatRp(totalJual - totalBeli - totalOps);

            document.getElementById('lr-penjualan').innerText = formatRp(totalJual);
            document.getElementById('lr-hpp').innerText = formatRp(totalBeli);
            document.getElementById('lr-ops').innerText = formatRp(totalOps);
            document.getElementById('lr-bersih').innerText = formatRp(totalJual - totalBeli - totalOps);
            
            document.getElementById('total-susut').innerText = Math.round(susutKg).toLocaleString() + ' Kg';
            document.getElementById('total-lossis').innerText = Math.round(totalLossisKg).toLocaleString() + ' Kg';
            document.getElementById('total-lossis-list').innerText = Math.round(totalLossisKg).toLocaleString() + ' Kg';
        }

        function updateUI() {
            const r = (id, data, tpl) => {
                const el = document.getElementById(id);
                if(el) el.innerHTML = data.slice().reverse().map(x => `<tr>${tpl(x)}</tr>`).join('');
            };

            r('list-pembelian', db.pembelian, x => `
                <td class="p-5 text-xs font-bold text-slate-400">${x.tgl}</td>
                <td class="p-5 font-bold">${x.pemasok}</td>
                <td class="p-5 font-black text-emerald-600">${Math.round(x.netto).toLocaleString()} Kg</td>
                <td class="p-5 text-xs">Rp ${x.harga.toLocaleString()}</td>
                <td class="p-5 font-black">${formatRp(x.total)}</td>
                <td class="p-5 text-right"><button onclick="deleteRecord('pembelian', ${x.id})" class="text-slate-300 hover:text-red-500"><i data-lucide="trash-2" class="w-4 h-4"></i></button></td>
            `);

            r('list-lossis', db.lossis, x => `
                <td class="p-5 text-xs font-bold text-slate-400">${x.tgl}</td>
                <td class="p-5 text-xs font-medium">${x.ket || '-'}</td>
                <td class="p-5"><span class="px-2 py-0.5 rounded-full text-[10px] font-black uppercase ${x.tipe==='kurang'?'bg-red-50 text-red-600':'bg-blue-50 text-blue-600'}">${x.tipe}</span></td>
                <td class="p-5 font-black ${x.tipe==='kurang'?'text-red-500':'text-blue-600'}">${x.netto.toLocaleString()} Kg</td>
                <td class="p-5 text-right"><button onclick="deleteRecord('lossis', ${x.id})" class="text-slate-300 hover:text-red-500"><i data-lucide="trash-2" class="w-4 h-4"></i></button></td>
            `);

            r('list-pengiriman', db.pengiriman, x => `
                <td class="p-5 text-xs font-bold text-slate-400">${x.tgl}</td>
                <td class="p-5 font-bold">${x.pks}</td>
                <td class="p-5 font-black text-blue-600">${x.netto.toLocaleString()} Kg</td>
                <td class="p-5"><span class="px-2.5 py-1 rounded-full text-[10px] font-black uppercase ${x.status==='Selesai'?'bg-emerald-50 text-emerald-600':'bg-blue-50 text-blue-600'}">${x.status}</span></td>
                <td class="p-5 text-right"><button onclick="deleteRecord('pengiriman', ${x.id})" class="text-slate-300 hover:text-red-500"><i data-lucide="trash-2" class="w-4 h-4"></i></button></td>
            `);

            r('list-penjualan', db.penjualan, x => `
                <td class="p-5 text-xs font-bold text-slate-400">${x.tgl}</td>
                <td class="p-5 font-black text-indigo-600">${Math.round(x.netto).toLocaleString()} Kg</td>
                <td class="p-5 text-xs">Rp ${x.harga.toLocaleString()}</td>
                <td class="p-5 font-black text-emerald-600">${formatRp(x.total)}</td>
                <td class="p-5 text-right"><button onclick="deleteRecord('penjualan', ${x.id})" class="text-slate-300 hover:text-red-500"><i data-lucide="trash-2" class="w-4 h-4"></i></button></td>
            `);

            r('list-modal', db.modal, x => `
                <td class="p-5 text-xs font-bold text-slate-400">${x.tgl}</td>
                <td class="p-5 font-medium">${x.ket}</td>
                <td class="p-5 text-right font-black text-emerald-600">${formatRp(x.amount)}</td>
                <td class="p-5 text-right"><button onclick="deleteRecord('modal', ${x.id})" class="text-slate-300 hover:text-red-500"><i data-lucide="trash-2" class="w-4 h-4"></i></button></td>
            `);

            r('list-operasional', db.operasional, x => `
                <td class="p-5 text-xs font-bold text-slate-400">${x.tgl}</td>
                <td class="p-5 font-medium">${x.ket}</td>
                <td class="p-5 text-right font-black text-orange-600">${formatRp(x.amount)}</td>
                <td class="p-5 text-right"><button onclick="deleteRecord('operasional', ${x.id})" class="text-slate-300 hover:text-red-500"><i data-lucide="trash-2" class="w-4 h-4"></i></button></td>
            `);

            updateDashboard();
            lucide.createIcons();
        }

        function exportToExcel() {
            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, XLSX.utils.json_to_sheet(db.pembelian), "Pembelian");
            XLSX.utils.book_append_sheet(wb, XLSX.utils.json_to_sheet(db.penjualan), "Penjualan PKS");
            XLSX.utils.book_append_sheet(wb, XLSX.utils.json_to_sheet(db.lossis), "Lossis Stok");
            XLSX.utils.book_append_sheet(wb, XLSX.utils.json_to_sheet(db.operasional), "Operasional");
            XLSX.writeFile(wb, `Laporan_TBS_PRO_Full_${new Date().toISOString().split('T')[0]}.xlsx`);
            notify("Laporan Excel diunduh!");
        }
    </script>
</body>
</html>
