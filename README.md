<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistem Peminjaman Tas Ruang TGCL</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Font Awesome CDN -->
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" rel="stylesheet">
    <!-- Google Font Inter -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f8fafc;
        }

        .modal {
            display: none;
            position: fixed;
            z-index: 50;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            overflow: auto;
            background-color: rgba(15, 23, 42, 0.6);
            backdrop-filter: blur(4px);
            align-items: center;
            justify-content: center;
        }

        .modal-active {
            display: flex;
        }

        .modal-content {
            animation: modalSlide 0.25s ease-out forwards;
        }

        @keyframes modalSlide {
            from {
                opacity: 0;
                transform: translateY(12px) scale(0.98);
            }
            to {
                opacity: 1;
                transform: translateY(0) scale(1);
            }
        }

        /* Custom Scrollbar */
        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #f1f5f9;
        }
        ::-webkit-scrollbar-thumb {
            background: #cbd5e1;
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #94a3b8;
        }
    </style>
</head>
<body class="text-slate-800 antialiased min-h-screen flex flex-col">

    <!-- Screen Login Overlay -->
    <div id="loginScreen" class="fixed inset-0 z-50 flex items-center justify-center bg-slate-900/80 backdrop-blur-sm">
        <div class="bg-white rounded-2xl shadow-2xl w-full max-w-md p-8 mx-4 relative">
            <div class="text-center mb-6">
                <div class="w-16 h-16 bg-blue-100 text-blue-600 rounded-full flex items-center justify-center mx-auto mb-3 text-2xl">
                    <i class="fas fa-user-lock"></i>
                </div>
                <h2 class="text-2xl font-bold text-slate-800">Login Sistem</h2>
                <p class="text-xs text-slate-500 mt-1">Sistem Peminjaman Tas Ruang TGCL</p>
            </div>

            <form id="loginForm" onsubmit="handleLogin(event)" class="space-y-4">
                <div id="loginError" class="hidden p-3 bg-rose-50 border border-rose-200 text-rose-600 rounded-lg text-xs font-medium text-center">
                    Username atau password salah!
                </div>
                <div>
                    <label for="loginUsername" class="block text-xs font-semibold text-slate-600 uppercase mb-1">Username</label>
                    <div class="relative">
                        <input type="text" id="loginUsername" required placeholder="Masukkan username" class="w-full pl-10 pr-3.5 py-2.5 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500">
                        <i class="fas fa-user absolute left-3.5 top-3 text-slate-400 text-sm"></i>
                    </div>
                </div>
                <div>
                    <label for="loginPassword" class="block text-xs font-semibold text-slate-600 uppercase mb-1">Password</label>
                    <div class="relative">
                        <input type="password" id="loginPassword" required placeholder="Masukkan password" class="w-full pl-10 pr-3.5 py-2.5 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500">
                        <i class="fas fa-lock absolute left-3.5 top-3 text-slate-400 text-sm"></i>
                    </div>
                </div>
                <button type="submit" class="w-full py-2.5 bg-blue-600 hover:bg-blue-700 text-white font-semibold text-sm rounded-lg transition shadow-md flex items-center justify-center gap-2 mt-2">
                    <i class="fas fa-sign-in-alt"></i> Masuk
                </button>
            </form>
        </div>
    </div>

    <!-- Main Application -->
    <main id="mainApp" class="container mx-auto px-4 py-6 max-w-7xl flex-1 hidden">
        
        <!-- Navigation Tabs & Actions -->
        <div class="flex flex-col md:flex-row justify-between items-start md:items-center mb-6 pb-4 border-b border-slate-200 gap-4">
            <div>
                <h1 class="text-2xl font-bold text-slate-900 tracking-tight">Sistem Peminjaman Tas Ruang TGCL</h1>
            </div>
            
            <div class="flex flex-wrap gap-2 items-center">
                <div class="inline-flex p-1 bg-slate-200/70 rounded-xl mr-2">
                    <button id="tabGridBtn" onclick="switchTab('grid')" class="px-4 py-2 text-sm font-semibold rounded-lg transition-all duration-150 bg-white text-blue-600 shadow-sm">
                        <i class="fas fa-th-large mr-2"></i>Daftar Loker
                    </button>
                    <button id="tabReportBtn" onclick="switchTab('report')" class="px-4 py-2 text-sm font-semibold rounded-lg transition-all duration-150 text-slate-600 hover:text-slate-900">
                        <i class="fas fa-calendar-alt mr-2"></i>Data Peminjaman
                    </button>
                </div>
                
                <!-- Fitur Penyimpanan: Backup & Restore -->
                <button onclick="backupData()" class="px-3.5 py-2 text-sm font-semibold rounded-lg transition-all duration-150 bg-emerald-100 text-emerald-700 hover:bg-emerald-200" title="Simpan data ke komputer">
                    <i class="fas fa-download mr-1.5"></i>Backup
                </button>
                <button onclick="document.getElementById('restoreFile').click()" class="px-3.5 py-2 text-sm font-semibold rounded-lg transition-all duration-150 bg-amber-100 text-amber-700 hover:bg-amber-200" title="Pulihkan data dari komputer">
                    <i class="fas fa-upload mr-1.5"></i>Restore
                </button>
                <input type="file" id="restoreFile" class="hidden" accept=".json" onchange="restoreData(event)">

                <button onclick="clearAllData()" class="px-3.5 py-2 text-sm font-semibold rounded-lg transition-all duration-150 bg-rose-100 text-rose-600 hover:bg-rose-200" title="Hapus semua data">
                    <i class="fas fa-trash-alt mr-1.5"></i>Reset
                </button>

                <!-- Tombol Logout -->
                <button onclick="handleLogout()" class="px-3.5 py-2 text-sm font-semibold rounded-lg transition-all duration-150 bg-slate-200 text-slate-700 hover:bg-slate-300" title="Keluar dari Sistem">
                    <i class="fas fa-sign-out-alt mr-1.5"></i>Logout
                </button>
            </div>
        </div>

        <!-- ================= TAB 1: GRID LOKER ================= -->
        <div id="tabGridContent" class="space-y-6">
            
            <!-- Controls & Summary Bar -->
            <div class="flex flex-col md:flex-row justify-between items-start md:items-center gap-4 bg-white p-4 rounded-xl shadow-sm border border-slate-200/80">
                <!-- Summary Badges -->
                <div class="flex items-center gap-6">
                    <div class="flex items-center gap-3">
                        <div class="w-10 h-10 rounded-lg bg-emerald-50 border border-emerald-200 text-emerald-600 flex items-center justify-center font-bold">
                            <i class="fas fa-door-open"></i>
                        </div>
                        <div>
                            <span class="text-xs font-semibold text-slate-400 uppercase tracking-wider block">Tersedia</span>
                            <span class="text-xl font-bold text-slate-800" id="count-tersedia">0</span>
                        </div>
                    </div>
                    <div class="h-8 w-px bg-slate-200"></div>
                    <div class="flex items-center gap-3">
                        <div class="w-10 h-10 rounded-lg bg-rose-50 border border-rose-200 text-rose-600 flex items-center justify-center font-bold">
                            <i class="fas fa-lock"></i>
                        </div>
                        <div>
                            <span class="text-xs font-semibold text-slate-400 uppercase tracking-wider block">Terpakai</span>
                            <span class="text-xl font-bold text-slate-800" id="count-terpakai">0</span>
                        </div>
                    </div>
                </div>

                <!-- Search & Status Filter -->
                <div class="flex w-full md:w-auto gap-2">
                    <div class="relative flex-1 md:w-64">
                        <input type="text" id="searchInput" placeholder="Cari nomor / peminjam..." class="w-full pl-9 pr-4 py-2 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500 transition">
                        <i class="fas fa-search absolute left-3 top-2.5 text-slate-400 text-sm"></i>
                    </div>
                    <select id="statusFilter" class="border border-slate-300 text-sm rounded-lg px-3 py-2 bg-white focus:outline-none focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500 cursor-pointer text-slate-700">
                        <option value="semua">Semua Status Loker</option>
                        <option value="tersedia">Tersedia</option>
                        <option value="terpakai">Terpakai</option>
                    </select>
                </div>
            </div>

            <!-- Grid Display -->
            <div id="lokerGrid" class="flex flex-wrap gap-3.5 justify-start">
                <!-- Data loker di-render di sini -->
            </div>
        </div>

        <!-- ================= TAB 2: DATA PEMINJAMAN HARIAN ================= -->
        <div id="tabReportContent" class="space-y-6 hidden">
            
            <!-- Filter Header Bar -->
            <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4 bg-white p-4 rounded-xl shadow-sm border border-slate-200/80">
                <div class="flex items-center gap-3">
                    <i class="fas fa-calendar-day text-blue-600 text-xl"></i>
                    <div>
                        <label for="reportDate" class="block text-xs font-semibold text-slate-500 uppercase tracking-wider">Pilih Tanggal Laporan</label>
                        <input type="date" id="reportDate" class="text-sm font-semibold text-slate-800 bg-slate-50 border border-slate-300 rounded-lg px-3 py-1.5 focus:outline-none focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500">
                    </div>
                </div>

                <div class="flex items-center gap-2 w-full sm:w-auto">
                    <div class="relative flex-1 sm:w-64">
                        <input type="text" id="reportSearch" placeholder="Cari di rekap harian..." class="w-full pl-9 pr-4 py-2 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500 transition">
                        <i class="fas fa-search absolute left-3 top-2.5 text-slate-400 text-sm"></i>
                    </div>
                    <button onclick="exportToCSV()" class="px-3.5 py-2 text-sm font-medium bg-slate-800 text-white rounded-lg hover:bg-slate-900 transition flex items-center gap-2">
                        <i class="fas fa-file-csv"></i><span class="hidden sm:inline">Export CSV</span>
                    </button>
                    <button onclick="window.print()" class="px-3.5 py-2 text-sm font-medium border border-slate-300 bg-white text-slate-700 rounded-lg hover:bg-slate-50 transition flex items-center gap-2">
                        <i class="fas fa-print"></i><span class="hidden sm:inline">Cetak</span>
                    </button>
                </div>
            </div>

            <!-- Daily Statistics Summary -->
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm flex items-center gap-4">
                    <div class="w-12 h-12 rounded-xl bg-blue-50 text-blue-600 flex items-center justify-center text-xl">
                        <i class="fas fa-list-check"></i>
                    </div>
                    <div>
                        <p class="text-xs font-semibold text-slate-400 uppercase tracking-wider">Total Peminjaman Hari Ini</p>
                        <p class="text-2xl font-bold text-slate-800" id="statDailyTotal">0</p>
                    </div>
                </div>
                <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm flex items-center gap-4">
                    <div class="w-12 h-12 rounded-xl bg-amber-50 text-amber-600 flex items-center justify-center text-xl">
                        <i class="fas fa-clock"></i>
                    </div>
                    <div>
                        <p class="text-xs font-semibold text-slate-400 uppercase tracking-wider">Sedang Dipinjam (Aktif)</p>
                        <p class="text-2xl font-bold text-slate-800" id="statDailyActive">0</p>
                    </div>
                </div>
                <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm flex items-center gap-4">
                    <div class="w-12 h-12 rounded-xl bg-emerald-50 text-emerald-600 flex items-center justify-center text-xl">
                        <i class="fas fa-check-circle"></i>
                    </div>
                    <div>
                        <p class="text-xs font-semibold text-slate-400 uppercase tracking-wider">Sudah Dikembalikan</p>
                        <p class="text-2xl font-bold text-slate-800" id="statDailyReturned">0</p>
                    </div>
                </div>
            </div>

            <!-- Report Table -->
            <div class="bg-white rounded-xl shadow-sm border border-slate-200 overflow-hidden">
                <div class="overflow-x-auto">
                    <table class="w-full text-left text-sm">
                        <thead class="bg-slate-50 border-b border-slate-200 text-slate-500 font-semibold text-xs uppercase tracking-wider">
                            <tr>
                                <th class="py-3 px-4">No. Loker</th>
                                <th class="py-3 px-4">Nama Peminjam</th>
                                <th class="py-3 px-4">Status Peminjam</th>
                                <th class="py-3 px-4">NIM / NIK / ID</th>
                                <th class="py-3 px-4">No. Telp</th>
                                <th class="py-3 px-4">Jam Pinjam</th>
                                <th class="py-3 px-4 text-center">Status</th>
                                <th class="py-3 px-4 text-right">Aksi</th>
                            </tr>
                        </thead>
                        <tbody id="reportTableBody" class="divide-y divide-slate-100 text-slate-700">
                            <!-- Logs will be populated here -->
                        </tbody>
                    </table>
                </div>
            </div>

        </div>

    </main>

    <!-- Modal 1: Form Peminjaman Baru -->
    <div id="borrowModal" class="modal">
        <div class="modal-content bg-white rounded-2xl shadow-2xl w-full max-w-md mx-4 overflow-hidden relative">
            <div class="bg-blue-600 px-6 py-4 flex justify-between items-center text-white">
                <h3 class="text-lg font-bold flex items-center gap-2">
                    <i class="fas fa-key"></i> Form Peminjaman Loker
                </h3>
                <button onclick="closeBorrowModal()" class="text-blue-100 hover:text-white transition">
                    <i class="fas fa-times text-lg"></i>
                </button>
            </div>

            <form id="borrowForm" class="p-6">
                <div class="mb-5 flex items-center justify-between bg-blue-50 p-4 rounded-xl border border-blue-100">
                    <span class="text-sm font-medium text-slate-600">Nomor Loker Terpilih:</span>
                    <span id="selectedLockerId" class="text-2xl font-extrabold text-blue-700">1</span>
                </div>

                <div class="space-y-4">
                    <div>
                        <label for="nama" class="block text-xs font-semibold text-slate-600 uppercase mb-1">Nama Lengkap</label>
                        <input type="text" id="nama" required placeholder="Masukkan nama peminjam" class="w-full px-3.5 py-2.5 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500">
                    </div>

                    <div>
                        <label for="statusPeminjam" class="block text-xs font-semibold text-slate-600 uppercase mb-1">Status Peminjam</label>
                        <select id="statusPeminjam" required class="w-full px-3.5 py-2.5 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500 bg-white">
                            <option value="Mahasiswa">Mahasiswa</option>
                            <option value="Dosen">Dosen</option>
                            <option value="Tendik">Tendik</option>
                            <option value="Siswa Sekolah">Siswa Sekolah</option>
                            <option value="Umum">Umum</option>
                        </select>
                    </div>
                    
                    <div class="grid grid-cols-2 gap-3">
                        <div>
                            <label for="nim" class="block text-xs font-semibold text-slate-600 uppercase mb-1">NIM / NIK / ID</label>
                            <input type="text" id="nim" required placeholder="Nomor Identitas" class="w-full px-3.5 py-2.5 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500">
                        </div>
                        <div>
                            <label for="telp" class="block text-xs font-semibold text-slate-600 uppercase mb-1">Nomor Telepon</label>
                            <input type="text" id="telp" required placeholder="Nomor Handphone" class="w-full px-3.5 py-2.5 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500">
                        </div>
                    </div>

                    <div>
                        <label for="tanggalPinjam" class="block text-xs font-semibold text-slate-600 uppercase mb-1">Tanggal</label>
                        <input type="date" id="tanggalPinjam" required class="w-full px-3.5 py-2.5 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500">
                    </div>
                </div>

                <div class="mt-7 flex justify-end gap-2.5">
                    <button type="button" onclick="closeBorrowModal()" class="px-4 py-2 border border-slate-300 text-slate-700 text-sm font-medium rounded-lg hover:bg-slate-50 transition">Batal</button>
                    <button type="submit" class="px-5 py-2 bg-blue-600 text-white text-sm font-semibold rounded-lg hover:bg-blue-700 transition shadow-sm flex items-center gap-2">
                        <i class="fas fa-check"></i> Konfirmasi Pinjam
                    </button>
                </div>
            </form>
        </div>
    </div>

    <!-- Modal 2: Info Detail / Pengembalian Loker -->
    <div id="returnModal" class="modal">
        <div class="modal-content bg-white rounded-2xl shadow-2xl w-full max-w-sm mx-4 overflow-hidden relative">
            <div class="bg-rose-600 px-6 py-4 flex justify-between items-center text-white">
                <h3 class="text-lg font-bold flex items-center gap-2">
                    <i class="fas fa-lock"></i> Detail Peminjaman
                </h3>
                <button onclick="closeReturnModal()" class="text-rose-100 hover:text-white transition">
                    <i class="fas fa-times text-lg"></i>
                </button>
            </div>
            
            <div class="p-6">
                <div class="text-center mb-5">
                    <div class="w-14 h-14 rounded-full bg-rose-100 text-rose-600 flex items-center justify-center text-2xl mx-auto mb-2">
                        <i class="fas fa-user-lock"></i>
                    </div>
                    <h2 class="text-2xl font-extrabold text-slate-800" id="returnLockerId">1</h2>
                    <span class="inline-block mt-1 px-2.5 py-0.5 bg-rose-100 text-rose-700 text-xs font-semibold rounded-full uppercase tracking-wider">Terpakai</span>
                </div>
                
                <div class="bg-slate-50 p-4 rounded-xl border border-slate-200/80 space-y-3 mb-6 text-sm">
                    <div>
                        <span class="text-[11px] font-bold uppercase tracking-wider text-slate-400 block">Peminjam</span>
                        <span class="font-bold text-slate-800" id="returnNama">-</span>
                    </div>
                    <div>
                        <span class="text-[11px] font-bold uppercase tracking-wider text-slate-400 block">Status Peminjam</span>
                        <span class="font-semibold text-blue-600" id="returnStatusPeminjam">-</span>
                    </div>
                    <div class="grid grid-cols-2 gap-2">
                        <div>
                            <span class="text-[11px] font-bold uppercase tracking-wider text-slate-400 block">NIM / NIK</span>
                            <span class="font-semibold text-slate-700" id="returnNim">-</span>
                        </div>
                        <div>
                            <span class="text-[11px] font-bold uppercase tracking-wider text-slate-400 block">No. Telepon</span>
                            <span class="font-semibold text-slate-700" id="returnTelp">-</span>
                        </div>
                    </div>
                    <div class="grid grid-cols-2 gap-2">
                        <div>
                            <span class="text-[11px] font-bold uppercase tracking-wider text-slate-400 block">Jam Pinjam</span>
                            <span class="font-semibold text-slate-700" id="returnWaktu">-</span>
                        </div>
                        <div>
                            <span class="text-[11px] font-bold uppercase tracking-wider text-slate-400 block">Tanggal</span>
                            <span class="font-semibold text-slate-700" id="returnTanggal">-</span>
                        </div>
                    </div>
                </div>

                <button id="confirmReturnBtn" class="w-full py-2.5 bg-rose-600 text-white rounded-lg hover:bg-rose-700 transition font-semibold text-sm flex justify-center items-center gap-2 shadow-sm">
                    <i class="fas fa-sign-out-alt"></i> Kembalikan Loker
                </button>
            </div>
        </div>
    </div>

    <!-- Modal 3: Pengubah Status Manual Loker -->
    <div id="manageModal" class="modal">
        <div class="modal-content bg-white rounded-2xl shadow-2xl w-full max-w-sm mx-4 overflow-hidden relative">
            <div class="bg-slate-800 px-6 py-4 flex justify-between items-center text-white">
                <h3 class="text-base font-bold flex items-center gap-2">
                    <i class="fas fa-cog"></i> Kelola Status Loker <span id="manageLockerIdText" class="text-blue-400">1</span>
                </h3>
                <button onclick="closeManageModal()" class="text-slate-400 hover:text-white transition">
                    <i class="fas fa-times text-lg"></i>
                </button>
            </div>
            <form id="manageForm" class="p-6">
                <div class="space-y-4">
                    <div>
                        <label class="block text-xs font-semibold text-slate-600 uppercase mb-1">Status Loker</label>
                        <select id="manageStatusSelect" class="w-full px-3 py-2 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500 bg-white">
                            <option value="tersedia">Tersedia</option>
                            <option value="terpakai">Terpakai</option>
                        </select>
                    </div>

                    <div id="manageBorrowerSection" class="space-y-3 pt-2 border-t border-slate-100 hidden">
                        <div>
                            <label class="block text-xs font-semibold text-slate-600 uppercase mb-1">Nama Peminjam</label>
                            <input type="text" id="manageNama" class="w-full px-3 py-2 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500">
                        </div>
                        <div>
                            <label class="block text-xs font-semibold text-slate-600 uppercase mb-1">Status Peminjam</label>
                            <select id="manageStatusPeminjam" class="w-full px-3 py-2 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500 bg-white">
                                <option value="Mahasiswa">Mahasiswa</option>
                                <option value="Dosen">Dosen</option>
                                <option value="Tendik">Tendik</option>
                                <option value="Siswa Sekolah">Siswa Sekolah</option>
                                <option value="Umum">Umum</option>
                            </select>
                        </div>
                        <div class="grid grid-cols-2 gap-2">
                            <div>
                                <label class="block text-xs font-semibold text-slate-600 uppercase mb-1">NIM / NIK / ID</label>
                                <input type="text" id="manageNim" class="w-full px-3 py-2 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500">
                            </div>
                            <div>
                                <label class="block text-xs font-semibold text-slate-600 uppercase mb-1">No. Telepon</label>
                                <input type="text" id="manageTelp" class="w-full px-3 py-2 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500/20 focus:border-blue-500">
                            </div>
                        </div>
                    </div>
                </div>

                <div class="mt-6 flex justify-end gap-2">
                    <button type="button" onclick="closeManageModal()" class="px-3.5 py-2 border border-slate-300 text-slate-700 text-sm font-medium rounded-lg hover:bg-slate-50 transition">Batal</button>
                    <button type="submit" class="px-4 py-2 bg-slate-800 text-white text-sm font-semibold rounded-lg hover:bg-slate-900 transition">Simpan Perubahan</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Modal Notification Pengganti Alert -->
    <div id="messageModal" class="modal z-[60]">
        <div class="modal-content bg-white rounded-2xl shadow-xl p-6 text-center max-w-sm mx-4">
            <div id="messageIcon" class="w-14 h-14 rounded-full mx-auto flex items-center justify-center text-2xl mb-3"></div>
            <h3 id="messageTitle" class="text-lg font-bold text-slate-800 mb-1">Pesan</h3>
            <div id="messageText" class="text-sm text-slate-600 mb-5"></div>
            <button onclick="closeMessageModal()" class="w-full py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition font-semibold text-sm">Tutup</button>
        </div>
    </div>

    <script>
        // Store Loker Data (1 - 90)
        let lokers = [];
        // Store Daily Transaction Logs
        let logs = [];

        let selectedLockerForBorrow = null;
        let selectedLockerForReturn = null;
        let selectedLockerForManage = null;

        // FUNGSI AUTENTIKASI (LOGIN & LOGOUT)
        function checkAuth() {
            const isLoggedIn = sessionStorage.getItem('isLoggedIn') === 'true';
            const loginScreen = document.getElementById('loginScreen');
            const mainContent = document.getElementById('mainApp');

            if (isLoggedIn) {
                loginScreen.classList.add('hidden');
                mainContent.classList.remove('hidden');
            } else {
                loginScreen.classList.remove('hidden');
                mainContent.classList.add('hidden');
            }
        }

        function handleLogin(e) {
            e.preventDefault();
            const username = document.getElementById('loginUsername').value;
            const password = document.getElementById('loginPassword').value;
            const errorEl = document.getElementById('loginError');

            if (username === 'ruangtgcl' && password === 'widodo') {
                sessionStorage.setItem('isLoggedIn', 'true');
                errorEl.classList.add('hidden');
                document.getElementById('loginForm').reset();
                checkAuth();
            } else {
                errorEl.classList.remove('hidden');
            }
        }

        function handleLogout() {
            sessionStorage.removeItem('isLoggedIn');
            checkAuth();
        }

        // FUNGSI PENYIMPANAN BROWSER (Local Storage)
        function saveData() {
            localStorage.setItem('dataLoker', JSON.stringify(lokers));
            localStorage.setItem('dataLogs', JSON.stringify(logs));
        }

        // Get Today's Date String Format YYYY-MM-DD
        function getTodayDateString() {
            const now = new Date();
            const year = now.getFullYear();
            const month = String(now.getMonth() + 1).padStart(2, '0');
            const day = String(now.getDate()).padStart(2, '0');
            return `${year}-${month}-${day}`;
        }

        // Get Current Time String Format HH:MM
        function getCurrentTimeString() {
            const now = new Date();
            return now.toTimeString().substring(0, 5);
        }

        function initData() {
            const today = getTodayDateString();
            
            // Coba ambil data dari LocalStorage terlebih dahulu
            const savedLokers = localStorage.getItem('dataLoker');
            const savedLogs = localStorage.getItem('dataLogs');

            if (savedLokers && savedLogs) {
                lokers = JSON.parse(savedLokers);
                logs = JSON.parse(savedLogs);
            } else {
                lokers = [];
                logs = [];

                for (let i = 1; i <= 90; i++) {
                    const id = i.toString();
                    lokers.push({ 
                        id: id, 
                        status: 'tersedia', 
                        peminjam: null 
                    });
                }

                saveData();
            }

            document.getElementById('reportDate').value = today;
            document.getElementById('tanggalPinjam').value = today;
        }

        // FITUR BACKUP DATA (Simpan ke File Fisik JSON)
        function backupData() {
            const dataToBackup = {
                lokers: lokers,
                logs: logs
            };
            const jsonString = JSON.stringify(dataToBackup, null, 2);
            const blob = new Blob([jsonString], { type: "application/json" });
            const url = URL.createObjectURL(blob);
            
            const link = document.createElement('a');
            link.href = url;
            link.download = `Backup_Loker_${getTodayDateString()}.json`;
            document.body.appendChild(link);
            link.click();
            
            document.body.removeChild(link);
            URL.revokeObjectURL(url);

            showMessage('Backup Berhasil', 'Data seluruh loker dan riwayat berhasil disimpan ke komputer Anda (File JSON).');
        }

        // FITUR RESTORE DATA (Ambil dari File Fisik JSON)
        function restoreData(event) {
            const file = event.target.files[0];
            if (!file) return;

            const reader = new FileReader();
            reader.onload = function(e) {
                try {
                    const parsedData = JSON.parse(e.target.result);
                    
                    if (parsedData.lokers && parsedData.logs) {
                        lokers = parsedData.lokers;
                        logs = parsedData.logs;
                        
                        saveData();
                        updateSummary();
                        filterGridData();
                        if (!document.getElementById('tabReportContent').classList.contains('hidden')) {
                            renderReport();
                        }

                        showMessage('Restore Berhasil', 'Seluruh data berhasil dipulihkan dari file backup.', 'success');
                    } else {
                        showMessage('Format Salah', 'File yang Anda unggah tidak sesuai. Gunakan file hasil Backup sistem ini.', 'warning');
                    }
                } catch (error) {
                    showMessage('Terjadi Kesalahan', 'Gagal membaca isi file. Pastikan file dalam format JSON yang valid.', 'warning');
                }
            };
            reader.readAsText(file);
            event.target.value = '';
        }

        function switchTab(tab) {
            const gridContent = document.getElementById('tabGridContent');
            const reportContent = document.getElementById('tabReportContent');
            const gridBtn = document.getElementById('tabGridBtn');
            const reportBtn = document.getElementById('tabReportBtn');

            if (tab === 'grid') {
                gridContent.classList.remove('hidden');
                reportContent.classList.add('hidden');
                gridBtn.className = "px-4 py-2 text-sm font-semibold rounded-lg transition-all duration-150 bg-white text-blue-600 shadow-sm";
                reportBtn.className = "px-4 py-2 text-sm font-semibold rounded-lg transition-all duration-150 text-slate-600 hover:text-slate-900";
            } else {
                gridContent.classList.add('hidden');
                reportContent.classList.remove('hidden');
                reportBtn.className = "px-4 py-2 text-sm font-semibold rounded-lg transition-all duration-150 bg-white text-blue-600 shadow-sm";
                gridBtn.className = "px-4 py-2 text-sm font-semibold rounded-lg transition-all duration-150 text-slate-600 hover:text-slate-900";
                renderReport();
            }
        }

        function renderLoker(dataToRender) {
            const gridEl = document.getElementById('lokerGrid');
            gridEl.innerHTML = '';

            if (dataToRender.length === 0) {
                gridEl.innerHTML = `
                    <div class="w-full py-12 text-center text-slate-400">
                        <i class="fas fa-inbox text-4xl mb-2 text-slate-300"></i>
                        <p class="text-sm">Tidak ada loker yang sesuai pencarian.</p>
                    </div>`;
                return;
            }

            dataToRender.forEach(loker => {
                const card = document.createElement('div');
                
                let borderClass, bgClass, iconClass, statusBadge, subText;

                if (loker.status === 'tersedia') {
                    bgClass = 'bg-white hover:bg-emerald-50/30';
                    borderClass = 'border-slate-200 hover:border-emerald-400';
                    iconClass = 'text-emerald-500';
                    statusBadge = '<span class="text-[8px] font-bold text-emerald-600 bg-emerald-50 border border-emerald-200 px-1 rounded-full uppercase">Tersedia</span>';
                    subText = '<span class="text-[9px] text-slate-400">Klik pinjam</span>';
                } else {
                    bgClass = 'bg-rose-50/20 hover:bg-rose-50/50';
                    borderClass = 'border-rose-200 hover:border-rose-300';
                    iconClass = 'text-rose-500';
                    statusBadge = '<span class="text-[8px] font-bold text-rose-600 bg-rose-50 border border-rose-200 px-1 rounded-full uppercase">Terpakai</span>';
                    const nama = loker.peminjam ? loker.peminjam.nama : '-';
                    subText = `<span class="text-[9px] font-medium text-slate-600 truncate w-full text-center" title="${nama}">${nama}</span>`;
                }

                card.className = `relative p-1 rounded-lg border ${borderClass} ${bgClass} transition-all duration-200 flex flex-col items-center justify-between w-[3cm] h-[3cm] shadow-sm group cursor-pointer shrink-0`;

                card.innerHTML = `
                    <div class="w-full flex justify-between items-center leading-none">
                        ${statusBadge}
                        <button onclick="openManageModal(event, '${loker.id}')" class="text-slate-300 hover:text-slate-600 p-0.5 rounded transition" title="Kelola Status">
                            <i class="fas fa-cog text-[10px]"></i>
                        </button>
                    </div>

                    <div class="flex flex-col items-center justify-center my-auto">
                        <i class="fas fa-lock ${iconClass} text-sm mb-0.5 group-hover:scale-110 transition-transform"></i>
                        <span class="text-sm font-bold text-slate-800 leading-none">${loker.id}</span>
                    </div>

                    ${subText}
                `;

                card.onclick = (e) => {
                    if (e.target.closest('button')) return;
                    if (loker.status === 'tersedia') {
                        openBorrowModal(loker.id);
                    } else {
                        openReturnModal(loker.id);
                    }
                };

                gridEl.appendChild(card);
            });
        }

        function updateSummary() {
            const countTersedia = lokers.filter(l => l.status === 'tersedia').length;
            const countTerpakai = lokers.filter(l => l.status === 'terpakai').length;

            document.getElementById('count-tersedia').textContent = countTersedia;
            document.getElementById('count-terpakai').textContent = countTerpakai;
        }

        function filterGridData() {
            const searchTerm = document.getElementById('searchInput').value.toLowerCase();
            const statusTerm = document.getElementById('statusFilter').value;

            const filtered = lokers.filter(l => {
                const matchSearch = l.id.toLowerCase().includes(searchTerm) || 
                                   (l.peminjam && l.peminjam.nama.toLowerCase().includes(searchTerm)) ||
                                   (l.peminjam && l.peminjam.statusPeminjam && l.peminjam.statusPeminjam.toLowerCase().includes(searchTerm)) ||
                                   (l.peminjam && l.peminjam.nim.toLowerCase().includes(searchTerm)) ||
                                   (l.peminjam && l.peminjam.telp && l.peminjam.telp.toLowerCase().includes(searchTerm));
                const matchStatus = statusTerm === 'semua' || l.status === statusTerm;
                return matchSearch && matchStatus;
            });

            renderLoker(filtered);
        }

        function renderReport() {
            const selectedDate = document.getElementById('reportDate').value;
            const searchVal = document.getElementById('reportSearch').value.toLowerCase();
            const tbody = document.getElementById('reportTableBody');

            tbody.innerHTML = '';

            const dayLogs = logs.filter(log => log.tanggal === selectedDate && (
                log.lockerId.toLowerCase().includes(searchVal) ||
                log.nama.toLowerCase().includes(searchVal) ||
                (log.statusPeminjam && log.statusPeminjam.toLowerCase().includes(searchVal)) ||
                log.nim.toLowerCase().includes(searchVal) ||
                (log.telp && log.telp.toLowerCase().includes(searchVal))
            ));

            const total = dayLogs.length;
            const active = dayLogs.filter(l => l.status === 'Aktif').length;
            const returned = dayLogs.filter(l => l.status === 'Selesai').length;

            document.getElementById('statDailyTotal').textContent = total;
            document.getElementById('statDailyActive').textContent = active;
            document.getElementById('statDailyReturned').textContent = returned;

            if (dayLogs.length === 0) {
                tbody.innerHTML = `
                    <tr>
                        <td colspan="8" class="py-8 text-center text-slate-400">
                            <i class="fas fa-folder-open text-3xl mb-2 text-slate-300"></i>
                            <p>Tidak ada data peminjaman pada tanggal ini.</p>
                        </td>
                    </tr>`;
                return;
            }

            dayLogs.forEach(log => {
                const tr = document.createElement('tr');
                tr.className = "hover:bg-slate-50 transition";

                const badge = log.status === 'Aktif' 
                    ? '<span class="px-2 py-0.5 text-xs font-semibold bg-amber-50 text-amber-700 border border-amber-200 rounded-full">Aktif</span>'
                    : '<span class="px-2 py-0.5 text-xs font-semibold bg-emerald-50 text-emerald-700 border border-emerald-200 rounded-full">Selesai</span>';

                const actionBtn = log.status === 'Aktif'
                    ? `<button onclick="returnFromReport('${log.lockerId}')" class="text-xs bg-rose-50 text-rose-600 hover:bg-rose-100 font-semibold px-2.5 py-1 rounded border border-rose-200 transition">Kembalikan</button>`
                    : '<span class="text-xs text-slate-400 font-medium">-</span>';

                tr.innerHTML = `
                    <td class="py-3 px-4 font-bold text-slate-900">Loker ${log.lockerId}</td>
                    <td class="py-3 px-4 font-semibold text-slate-800">${log.nama}</td>
                    <td class="py-3 px-4 font-medium text-blue-600">${log.statusPeminjam || '-'}</td>
                    <td class="py-3 px-4 text-slate-500">${log.nim}</td>
                    <td class="py-3 px-4 text-slate-500">${log.telp || '-'}</td>
                    <td class="py-3 px-4 text-slate-600">${log.jam}</td>
                    <td class="py-3 px-4 text-center">${badge}</td>
                    <td class="py-3 px-4 text-right">${actionBtn}</td>
                `;
                tbody.appendChild(tr);
            });
        }

        function openBorrowModal(id) {
            selectedLockerForBorrow = id;
            document.getElementById('selectedLockerId').textContent = id;
            document.getElementById('borrowForm').reset();
            document.getElementById('tanggalPinjam').value = getTodayDateString();
            document.getElementById('borrowModal').classList.add('modal-active');
        }

        function closeBorrowModal() {
            document.getElementById('borrowModal').classList.remove('modal-active');
            selectedLockerForBorrow = null;
        }

        function openReturnModal(id) {
            selectedLockerForReturn = id;
            const loker = lokers.find(l => l.id === id);
            if (!loker || !loker.peminjam) return;

            document.getElementById('returnLockerId').textContent = id;
            document.getElementById('returnNama').textContent = loker.peminjam.nama;
            document.getElementById('returnStatusPeminjam').textContent = loker.peminjam.statusPeminjam || '-';
            document.getElementById('returnNim').textContent = loker.peminjam.nim;
            document.getElementById('returnTelp').textContent = loker.peminjam.telp || '-';
            document.getElementById('returnWaktu').textContent = loker.peminjam.jam || '-';
            document.getElementById('returnTanggal').textContent = loker.peminjam.tanggal || '-';

            document.getElementById('returnModal').classList.add('modal-active');
        }

        function closeReturnModal() {
            document.getElementById('returnModal').classList.remove('modal-active');
            selectedLockerForReturn = null;
        }

        function openManageModal(e, id) {
            e.stopPropagation();
            selectedLockerForManage = id;
            const loker = lokers.find(l => l.id === id);

            document.getElementById('manageLockerIdText').textContent = id;
            document.getElementById('manageStatusSelect').value = loker.status;

            const section = document.getElementById('manageBorrowerSection');
            if (loker.status === 'terpakai') {
                section.classList.remove('hidden');
                document.getElementById('manageNama').value = loker.peminjam ? loker.peminjam.nama : '';
                document.getElementById('manageStatusPeminjam').value = loker.peminjam && loker.peminjam.statusPeminjam ? loker.peminjam.statusPeminjam : 'Mahasiswa';
                document.getElementById('manageNim').value = loker.peminjam ? loker.peminjam.nim : '';
                document.getElementById('manageTelp').value = loker.peminjam ? (loker.peminjam.telp || '') : '';
            } else {
                section.classList.add('hidden');
                document.getElementById('manageNama').value = '';
                document.getElementById('manageNim').value = '';
                document.getElementById('manageTelp').value = '';
            }

            document.getElementById('manageModal').classList.add('modal-active');
        }

        function closeManageModal() {
            document.getElementById('manageModal').classList.remove('modal-active');
            selectedLockerForManage = null;
        }

        function showMessage(title, text, type = 'success') {
            const msgIcon = document.getElementById('messageIcon');
            document.getElementById('messageTitle').textContent = title;
            document.getElementById('messageText').innerHTML = text;

            if (type === 'success') {
                msgIcon.className = 'w-14 h-14 rounded-full mx-auto flex items-center justify-center text-2xl mb-3 bg-emerald-100 text-emerald-600';
                msgIcon.innerHTML = '<i class="fas fa-check"></i>';
            } else {
                msgIcon.className = 'w-14 h-14 rounded-full mx-auto flex items-center justify-center text-2xl mb-3 bg-amber-100 text-amber-600';
                msgIcon.innerHTML = '<i class="fas fa-exclamation-triangle"></i>';
            }

            document.getElementById('messageModal').classList.add('modal-active');
        }

        function closeMessageModal() {
            document.getElementById('messageModal').classList.remove('modal-active');
        }

        // Fungsi Reset Data dengan Proteksi Password 's4lm4'
        function clearAllData() {
            const inputPassword = prompt("Masukkan password untuk melakukan reset data:");
            if (inputPassword === null) return;

            if (inputPassword === "s4lm4") {
                if (confirm("Password benar. Apakah Anda yakin ingin menghapus semua data peminjaman? Tindakan ini tidak dapat dibatalkan.")) {
                    localStorage.removeItem('dataLoker');
                    localStorage.removeItem('dataLogs');
                    initData();
                    updateSummary();
                    filterGridData();
                    renderReport();
                    showMessage('Data Direset', 'Semua data peminjaman dan loker telah kembali ke kondisi awal.', 'success');
                }
            } else {
                showMessage('Akses Ditolak', 'Password yang Anda masukkan salah!', 'warning');
            }
        }

        // Form Borrow Submission
        document.getElementById('borrowForm').addEventListener('submit', (e) => {
            e.preventDefault();
            const nama = document.getElementById('nama').value;
            const statusPeminjam = document.getElementById('statusPeminjam').value;
            const nim = document.getElementById('nim').value;
            const telp = document.getElementById('telp').value;
            const tanggal = document.getElementById('tanggalPinjam').value;
            const jam = getCurrentTimeString();

            const idx = lokers.findIndex(l => l.id === selectedLockerForBorrow);
            if (idx !== -1 && lokers[idx].status === 'tersedia') {
                lokers[idx].status = 'terpakai';
                lokers[idx].peminjam = { nama, statusPeminjam, nim, telp, tanggal, jam };

                logs.unshift({
                    id: Date.now(),
                    lockerId: selectedLockerForBorrow,
                    nama,
                    statusPeminjam,
                    nim,
                    telp,
                    tanggal,
                    jam,
                    status: 'Aktif'
                });

                saveData();

                closeBorrowModal();
                updateSummary();
                filterGridData();

                showMessage('Peminjaman Berhasil', `Loker <b class="text-blue-600">${selectedLockerForBorrow}</b> berhasil dipinjam atas nama <b>${nama}</b> (${statusPeminjam}).`);
            }
        });

        // Confirm Locker Return
        document.getElementById('confirmReturnBtn').addEventListener('click', () => {
            const id = selectedLockerForReturn;
            const idx = lokers.findIndex(l => l.id === id);

            if (idx !== -1 && lokers[idx].status === 'terpakai') {
                lokers[idx].status = 'tersedia';
                lokers[idx].peminjam = null;

                const activeLog = logs.find(l => l.lockerId === id && l.status === 'Aktif');
                if (activeLog) {
                    activeLog.status = 'Selesai';
                }

                saveData();

                closeReturnModal();
                updateSummary();
                filterGridData();

                showMessage('Loker Dikembalikan', `Loker <b class="text-emerald-600">${id}</b> sudah kosong dan siap digunakan kembali.`);
            }
        });

        function returnFromReport(lockerId) {
            selectedLockerForReturn = lockerId;
            document.getElementById('confirmReturnBtn').click();
            renderReport();
        }

        document.getElementById('manageStatusSelect').addEventListener('change', (e) => {
            const section = document.getElementById('manageBorrowerSection');
            if (e.target.value === 'terpakai') {
                section.classList.remove('hidden');
            } else {
                section.classList.add('hidden');
            }
        });

        document.getElementById('manageForm').addEventListener('submit', (e) => {
            e.preventDefault();
            const idx = lokers.findIndex(l => l.id === selectedLockerForManage);
            if (idx === -1) return;

            const newStatus = document.getElementById('manageStatusSelect').value;

            if (newStatus === 'terpakai') {
                const nama = document.getElementById('manageNama').value || 'Peminjam Manual';
                const statusPeminjam = document.getElementById('manageStatusPeminjam').value || 'Umum';
                const nim = document.getElementById('manageNim').value || '-';
                const telp = document.getElementById('manageTelp').value || '-';
                const tanggal = getTodayDateString();
                const jam = getCurrentTimeString();

                lokers[idx].status = 'terpakai';
                lokers[idx].peminjam = { nama, statusPeminjam, nim, telp, tanggal, jam };

                logs.unshift({
                    id: Date.now(),
                    lockerId: selectedLockerForManage,
                    nama,
                    statusPeminjam,
                    nim,
                    telp,
                    tanggal,
                    jam,
                    status: 'Aktif'
                });
            } else {
                lokers[idx].status = 'tersedia';
                lokers[idx].peminjam = null;

                const activeLog = logs.find(l => l.lockerId === selectedLockerForManage && l.status === 'Aktif');
                if (activeLog) activeLog.status = 'Selesai';
            }

            saveData();

            closeManageModal();
            updateSummary();
            filterGridData();
            showMessage('Status Diperbarui', `Status Loker <b>${selectedLockerForManage}</b> telah diubah.`);
        });

        function exportToCSV() {
            const selectedDate = document.getElementById('reportDate').value;
            const dayLogs = logs.filter(log => log.tanggal === selectedDate);

            if (dayLogs.length === 0) {
                showMessage('Export Gagal', 'Tidak ada data untuk diexport pada tanggal terpilih.', 'warning');
                return;
            }

            let csvContent = "data:text/csv;charset=utf-8,";
            csvContent += "No Loker,Nama Peminjam,Status Peminjam,NIM/ID,No Telp,Tanggal,Jam Pinjam,Status\n";

            dayLogs.forEach(row => {
                csvContent += `"${row.lockerId}","${row.nama}","${row.statusPeminjam || '-'}","${row.nim}","${row.telp || '-'}","${row.tanggal}","${row.jam}","${row.status}"\n`;
            });

            const encodedUri = encodeURI(csvContent);
            const link = document.createElement("a");
            link.setAttribute("href", encodedUri);
            link.setAttribute("download", `Laporan_Peminjaman_Loker_${selectedDate}.csv`);
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
        }

        window.addEventListener('DOMContentLoaded', () => {
            checkAuth();
            initData();
            updateSummary();
            renderLoker(lokers);

            document.getElementById('searchInput').addEventListener('input', filterGridData);
            document.getElementById('statusFilter').addEventListener('change', filterGridData);
            document.getElementById('reportDate').addEventListener('change', renderReport);
            document.getElementById('reportSearch').addEventListener('input', renderReport);
        });
    </script>
</body>
</html>
