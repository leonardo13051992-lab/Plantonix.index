<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <meta name="theme-color" content="#0A0A0A">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <title>PLANTONIX - Gestão de Escalas</title>
    
    <link href="https://fonts.cdnfonts.com/css/uber-move" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800;900&display=swap" rel="stylesheet">
    
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    
    <style>
        * { margin:0; padding:0; box-sizing:border-box; font-family:'Uber Move','Inter',sans-serif; -webkit-tap-highlight-color:transparent; }
        
        :root {
            --black:#0A0A0A; --white:#FFFFFF; --galaxy-primary:#1E3A8A; --galaxy-secondary:#2563EB;
            --galaxy-tertiary:#3B82F6; --galaxy-soft:#1D4ED8; --gray-light:#F5F5F5; --gray-dark:#1F2937;
            --bg-primary:var(--white); --bg-secondary:var(--gray-light); --bg-tertiary:#F8F9FA;
            --text-primary:var(--black); --text-secondary:#1F2937; --text-tertiary:#6B7280;
            --text-quaternary:#9CA3AF; --text-light:var(--white);
            --border-light:#E5E7EB; --border-medium:#D1D5DB;
            --shadow-sm:0 1px 3px rgba(0,0,0,0.06); --shadow-md:0 4px 16px rgba(0,0,0,0.08);
            --shadow-lg:0 8px 32px rgba(0,0,0,0.12); --shadow-xl:0 20px 60px rgba(0,0,0,0.16);
            --gradient-header:linear-gradient(135deg,#0A0A0A 0%,#1E3A8A 50%,#2563EB 100%);
            --gradient-menu:linear-gradient(180deg,#0A0A0A 0%,#1E3A8A 50%,#2563EB 100%);
            --gradient-primary:linear-gradient(135deg,#1E3A8A,#2563EB);
            --gradient-whatsapp:linear-gradient(135deg,#1D4ED8,#2563EB);
            --radius-sm:8px; --radius-md:12px; --radius-lg:16px; --radius-xl:20px; --radius-full:9999px;
            --transition:0.3s cubic-bezier(0.4,0,0.2,1); --header-height:56px;
            --success:#22C55E; --danger:#EF4444;
        }
        
        [data-theme="dark"] {
            --bg-primary:var(--black); --bg-secondary:#111827; --bg-tertiary:#1A1A2E;
            --text-primary:var(--white); --text-secondary:#E5E7EB;
            --text-tertiary:#9CA3AF; --text-quaternary:#6B7280;
            --border-light:#1F2937; --border-medium:#374151;
            --shadow-sm:0 1px 3px rgba(0,0,0,0.4); --shadow-md:0 4px 16px rgba(0,0,0,0.5);
            --shadow-lg:0 8px 32px rgba(0,0,0,0.6); --shadow-xl:0 20px 60px rgba(0,0,0,0.7);
        }
        
        body { background:var(--bg-secondary); min-height:100vh; display:flex; justify-content:center; color:var(--text-primary); transition:background .3s,color .3s; }
        .svg-icon { width:18px; height:18px; flex-shrink:0; display:inline-block; }
        .svg-icon path { fill:currentColor; stroke:currentColor; }
        
        /* SPLASH */
        #splashScreen { position:fixed; top:0; left:0; width:100%; height:100%; background:var(--white); display:flex; flex-direction:column; align-items:center; justify-content:center; z-index:99999; transition:opacity .6s; }
        #splashScreen.hide { opacity:0; pointer-events:none; }
        #splashScreen .splash-logo { font-size:48px; font-weight:300; color:var(--black); letter-spacing:6px; }
        #splashScreen .splash-logo span { font-weight:700; color:var(--galaxy-primary); }
        #splashScreen .splash-subtitle { font-size:13px; color:#6B7280; margin-top:12px; letter-spacing:4px; text-transform:uppercase; font-weight:300; }
        #splashScreen .splash-loader { margin-top:40px; width:40px; height:3px; background:#E5E7EB; border-radius:3px; overflow:hidden; }
        #splashScreen .splash-loader .splash-loader-bar { width:50%; height:100%; background:var(--galaxy-primary); border-radius:3px; animation:loading 1.2s ease-in-out infinite; }
        #splashScreen .splash-version { position:absolute; bottom:30px; font-size:11px; color:#D1D5DB; letter-spacing:2px; font-weight:300; }
        @keyframes loading { 0% { transform:translateX(-100%); } 100% { transform:translateX(300%); } }
        
        /* MENU */
        .menu-overlay { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:rgba(0,0,0,0.5); z-index:60; opacity:0; transition:opacity .3s; backdrop-filter:blur(4px); }
        .menu-overlay.active { display:block; opacity:1; }
        .side-menu { position:fixed; top:0; left:-290px; width:290px; height:100%; background:var(--gradient-menu); z-index:70; transition:left .3s cubic-bezier(0.4,0,0.2,1); box-shadow:var(--shadow-xl); overflow-y:auto; border-radius:0 var(--radius-lg) var(--radius-lg) 0; }
        .side-menu.open { left:0; }
        .side-menu-header { padding:32px 24px 20px; border-bottom:1px solid rgba(255,255,255,0.06); }
        .side-menu-header .menu-brand { font-size:20px; font-weight:300; color:var(--white); letter-spacing:3px; }
        .side-menu-header .menu-brand span { font-weight:700; color:var(--white); }
        .side-menu-header .menu-sub { font-size:10px; color:rgba(255,255,255,0.4); letter-spacing:2px; text-transform:uppercase; margin-top:4px; font-weight:300; }
        .side-menu-close { position:absolute; top:16px; right:16px; background:none; border:none; color:rgba(255,255,255,0.4); font-size:24px; cursor:pointer; padding:4px 8px; transition:color .2s; }
        .side-menu-close:hover { color:var(--white); }
        .side-menu-items { padding:8px 0; }
        .side-menu-item { display:flex; align-items:center; gap:14px; padding:12px 24px; border:none; background:none; width:100%; text-align:left; font-size:14px; font-weight:500; color:rgba(255,255,255,0.7); cursor:pointer; transition:all .15s; border-radius:0; }
        .side-menu-item:hover { background:rgba(255,255,255,0.06); color:var(--white); }
        .side-menu-item:active { background:rgba(255,255,255,0.1); }
        .side-menu-item.active { background:rgba(255,255,255,0.08); color:var(--white); border-left:3px solid var(--white); }
        .side-menu-item .menu-icon { width:20px; height:20px; flex-shrink:0; display:flex; align-items:center; justify-content:center; }
        .side-menu-item .menu-icon svg { width:18px; height:18px; }
        .side-menu-item .menu-icon svg path { stroke:currentColor; }
        .side-menu-divider { height:1px; background:rgba(255,255,255,0.06); margin:4px 16px; }
        .side-menu-footer { padding:16px 24px; border-top:1px solid rgba(255,255,255,0.06); font-size:10px; color:rgba(255,255,255,0.3); text-align:center; letter-spacing:1px; font-weight:300; }
        .side-menu-footer .powered span { font-weight:700; color:rgba(255,255,255,0.6); }
        
        /* LOGIN */
        .login-screen { display:none; justify-content:center; align-items:center; min-height:100vh; padding:20px; width:100%; }
        .login-screen.active { display:flex; }
        .login-box { background:var(--bg-primary); border-radius:var(--radius-xl); padding:40px 32px; max-width:400px; width:100%; box-shadow:var(--shadow-lg); transition:background .3s; }
        .logo { text-align:center; margin-bottom:32px; }
        .logo h1 { font-size:32px; font-weight:300; color:var(--text-primary); letter-spacing:4px; }
        .logo h1 span { font-weight:700; color:var(--galaxy-primary); }
        .logo p { color:var(--text-tertiary); font-size:13px; margin-top:4px; letter-spacing:2px; text-transform:uppercase; font-weight:300; }
        .form-group { margin-bottom:16px; }
        .form-group label { display:block; font-size:12px; font-weight:600; color:var(--text-secondary); margin-bottom:4px; }
        .form-group input { width:100%; padding:12px 16px; border:2px solid var(--border-light); border-radius:var(--radius-md); font-size:14px; outline:none; background:var(--bg-primary); color:var(--text-primary); transition:border-color .2s,box-shadow .2s; font-weight:500; }
        .form-group input:focus { border-color:var(--galaxy-secondary); box-shadow:0 0 0 4px rgba(37,99,235,0.08); }
        .password-wrapper { display:flex; align-items:center; border:2px solid var(--border-light); border-radius:var(--radius-md); background:var(--bg-primary); transition:border-color .2s,box-shadow .2s; }
        .password-wrapper:focus-within { border-color:var(--galaxy-secondary); box-shadow:0 0 0 4px rgba(37,99,235,0.08); }
        .password-wrapper input { border:none; flex:1; padding:12px 16px; font-size:14px; outline:none; background:transparent; color:var(--text-primary); font-weight:500; }
        .password-toggle { background:none; border:none; padding:8px 12px; cursor:pointer; color:var(--text-tertiary); display:flex; align-items:center; justify-content:center; transition:color .2s; }
        .password-toggle:hover { color:var(--text-primary); }
        .btn-login { width:100%; padding:14px; background:var(--gradient-primary); color:white; border:none; border-radius:var(--radius-md); font-size:14px; font-weight:600; cursor:pointer; transition:transform .15s,opacity .15s,box-shadow .2s; letter-spacing:.5px; box-shadow:0 4px 16px rgba(30,58,138,0.25); }
        .btn-login:active { transform:scale(0.97); }
        .btn-login:disabled { opacity:0.6; cursor:not-allowed; }
        .btn-login:hover { box-shadow:0 4px 24px rgba(30,58,138,0.35); }
        .login-error { color:var(--danger); font-size:13px; text-align:center; margin-top:12px; display:none; }
        .login-footer { text-align:center; margin-top:20px; font-size:13px; color:var(--text-tertiary); }
        
        /* APP */
        .app-container { max-width:480px; width:100%; min-height:100vh; background:var(--bg-primary); padding:0 16px 20px; display:none; flex-direction:column; margin:0 auto; transition:background .3s; }
        .app-container.active { display:flex; }
        .app-header { display:flex; align-items:center; justify-content:space-between; padding:12px 0 10px; margin:0 -16px; padding-left:16px; padding-right:16px; background:var(--gradient-header); position:sticky; top:0; z-index:10; border-bottom:1px solid rgba(255,255,255,0.06); box-shadow:0 2px 20px rgba(0,0,0,0.15); }
        .menu-icon { cursor:pointer; padding:8px; background:none; border:none; width:44px; height:44px; border-radius:var(--radius-sm); transition:background .2s,color .2s; display:flex; align-items:center; justify-content:center; color:rgba(255,255,255,0.7); }
        .menu-icon:hover { background:rgba(255,255,255,0.06); color:var(--white); }
        .menu-icon:active { background:rgba(255,255,255,0.1); }
        .app-title { text-align:center; flex:1; }
        .app-title .main { font-size:20px; font-weight:300; color:var(--white); letter-spacing:5px; text-transform:uppercase; display:block; }
        .app-title .main span { font-weight:700; color:var(--white); }
        .app-title .sub { font-size:9px; font-weight:300; color:rgba(255,255,255,0.4); letter-spacing:3px; text-transform:uppercase; display:block; margin-top:2px; }
        .header-actions { display:flex; align-items:center; gap:8px; }
        .theme-toggle, .logout-btn { background:none; border:none; cursor:pointer; padding:8px; color:rgba(255,255,255,0.5); width:44px; height:44px; border-radius:var(--radius-sm); transition:all .2s; display:flex; align-items:center; justify-content:center; }
        .theme-toggle:hover, .logout-btn:hover { background:rgba(255,255,255,0.06); color:var(--white); }
        .theme-toggle:active, .logout-btn:active { background:rgba(255,255,255,0.1); }
        
        .brand-area { text-align:center; padding:16px 0; margin:0 -16px; padding-left:16px; padding-right:16px; background:var(--bg-secondary); border-bottom:1px solid var(--border-light); transition:background .3s,border-color .3s; }
        .brand-name { font-size:20px; font-weight:700; color:var(--text-primary); letter-spacing:2px; }
        .brand-name .highlight { color:var(--galaxy-primary); }
        .brand-subname { font-size:12px; font-weight:300; color:var(--text-tertiary); margin-top:4px; cursor:pointer; transition:color .3s; letter-spacing:1px; }
        .brand-subname .edit-icon { font-size:12px; opacity:0.3; }
        .brand-powered { font-size:9px; color:var(--text-quaternary); margin-top:6px; letter-spacing:1px; font-weight:300; }
        .brand-powered span { font-weight:600; color:var(--galaxy-primary); }
        
        .action-buttons { display:flex; gap:10px; justify-content:center; margin:16px 0 14px; }
        .action-btn { flex:1; max-width:180px; padding:10px 16px; border:2px solid var(--border-light); border-radius:var(--radius-md); background:var(--bg-primary); color:var(--text-secondary); font-size:12px; font-weight:500; cursor:pointer; transition:all .2s; display:flex; align-items:center; justify-content:center; gap:8px; letter-spacing:.3px; }
        .action-btn:hover { border-color:var(--galaxy-secondary); color:var(--galaxy-secondary); background:rgba(37,99,235,0.04); }
        .action-btn:active { transform:scale(0.96); }
        .action-btn .svg-icon { width:16px; height:16px; }
        
        .share-buttons { display:flex; gap:8px; margin:12px 0 14px; justify-content:center; flex-wrap:wrap; }
        .share-btn { padding:10px 16px; border:none; border-radius:var(--radius-md); font-size:12px; font-weight:500; cursor:pointer; display:flex; align-items:center; justify-content:center; gap:6px; transition:all .2s; color:white; letter-spacing:.2px; box-shadow:var(--shadow-sm); flex:1; min-width:100px; max-width:160px; height:42px; }
        .share-btn:hover { transform:translateY(-2px); box-shadow:var(--shadow-md); }
        .share-btn:active { transform:scale(0.96); }
        .share-btn.whatsapp { background:var(--gradient-whatsapp); }
        .share-btn.whatsapp:hover { background:linear-gradient(135deg,#15307A,#1D4ED8); }
        .share-btn.png { background:var(--galaxy-primary); }
        .share-btn.png:hover { background:#15307A; }
        .share-btn.pdf { background:var(--gradient-primary); }
        .share-btn.pdf:hover { background:linear-gradient(135deg,#15307A,#1D4ED8); }
        .share-btn svg { width:16px; height:16px; flex-shrink:0; fill:currentColor; }
        .share-btn span { white-space:nowrap; }
        
        .client-selector { margin:0 0 14px; }
        .client-selector label { display:block; font-size:11px; font-weight:600; color:var(--text-tertiary); margin-bottom:6px; text-transform:uppercase; letter-spacing:1px; }
        .client-selector select { width:100%; padding:12px 16px; border:2px solid var(--border-light); border-radius:var(--radius-md); font-size:14px; font-weight:500; background:var(--bg-primary); color:var(--text-primary); outline:none; transition:border-color .2s,box-shadow .2s; cursor:pointer; -webkit-appearance:none; appearance:none; background-image:url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='12' height='12' viewBox='0 0 12 12'%3E%3Cpath fill='%236B7280' d='M6 8L1 3h10z'/%3E%3C/svg%3E"); background-repeat:no-repeat; background-position:right 16px center; }
        .client-selector select:focus { border-color:var(--galaxy-secondary); box-shadow:0 0 0 4px rgba(37,99,235,0.08); }
        [data-theme="dark"] .client-selector select { background-image:url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='12' height='12' viewBox='0 0 12 12'%3E%3Cpath fill='%239CA3AF' d='M6 8L1 3h10z'/%3E%3C/svg%3E"); }
        
        .client-info-card { background:var(--bg-secondary); border-radius:var(--radius-lg); padding:16px 18px; margin-bottom:12px; border:1px solid var(--border-light); transition:all .3s; box-shadow:var(--shadow-sm); }
        .client-info-card:hover { border-color:var(--galaxy-secondary); box-shadow:var(--shadow-md); }
        .client-info-row { display:flex; padding:4px 0; }
        .client-info-label { font-weight:600; color:var(--text-tertiary); width:80px; flex-shrink:0; font-size:11px; text-transform:uppercase; letter-spacing:.5px; }
        .client-info-value { flex:1; color:var(--text-primary); font-size:13px; word-break:break-word; font-weight:500; }
        .client-info-value.placeholder { color:var(--text-quaternary); font-weight:400; font-style:italic; }
        .client-info-value.italic { font-style:italic; }
        
        .stats-grid { display:grid; grid-template-columns:repeat(3,1fr); gap:8px; margin-bottom:14px; }
        .stat-card { background:var(--bg-secondary); border-radius:var(--radius-lg); padding:14px 8px; text-align:center; border:1px solid var(--border-light); transition:all .3s; box-shadow:var(--shadow-sm); }
        .stat-card:hover { border-color:var(--galaxy-secondary); transform:translateY(-1px); box-shadow:var(--shadow-md); }
        .stat-number { font-size:22px; font-weight:700; color:var(--text-primary); }
        .stat-label { font-size:9px; color:var(--text-tertiary); text-transform:uppercase; letter-spacing:.5px; margin-top:4px; font-weight:500; }
        
        .calendar-header { display:flex; align-items:center; justify-content:space-between; padding:8px 0 12px; }
        .calendar-nav { background:none; border:none; padding:8px 12px; cursor:pointer; color:var(--text-secondary); border-radius:var(--radius-sm); transition:all .2s; font-size:16px; }
        .calendar-nav:hover { background:var(--bg-secondary); color:var(--galaxy-secondary); }
        .calendar-nav:active { background:var(--bg-tertiary); }
        .calendar-title { font-size:18px; font-weight:700; color:var(--text-primary); }
        
        .calendar-grid { display:grid; grid-template-columns:repeat(7,1fr); gap:6px; margin-bottom:8px; }
        .day-name { text-align:center; font-weight:600; color:var(--text-secondary); font-size:11px; padding:10px 0 8px; text-transform:uppercase; letter-spacing:.5px; background:var(--bg-tertiary); border-radius:var(--radius-sm); }
        .day-cell { border-radius:var(--radius-sm); padding:6px 3px; min-height:68px; border:2px solid var(--border-light); display:flex; flex-direction:column; align-items:center; justify-content:flex-start; gap:2px; background:var(--bg-primary); cursor:default; transition:all .15s; position:relative; box-shadow:var(--shadow-sm); }
        .day-cell:hover { border-color:var(--border-medium); transform:translateY(-1px); box-shadow:var(--shadow-md); }
        .day-cell:active { transform:scale(0.95); }
        .day-cell .day-number { font-weight:700; font-size:14px; align-self:flex-start; margin-left:4px; margin-top:2px; color:var(--text-primary); }
        .day-cell .employee-name { font-size:9px; font-weight:600; width:100%; text-align:center; padding:3px 4px; border-radius:4px; overflow:hidden; white-space:nowrap; text-overflow:ellipsis; background:var(--bg-secondary); color:var(--text-primary); border:1px solid var(--border-light); transition:all .2s; }
        .day-cell .employee-name span { overflow:hidden; text-overflow:ellipsis; white-space:nowrap; color:var(--text-primary); }
        .day-cell.empty { background:transparent !important; border:none; min-height:unset; box-shadow:none; }
        .day-cell.today { border-color:var(--galaxy-secondary) !important; border-width:2px; box-shadow:0 0 0 3px rgba(37,99,235,0.12),var(--shadow-md) !important; }
        .day-cell.today .today-badge { position:absolute; top:-6px; right:-6px; background:var(--galaxy-secondary); color:white; font-size:7px; font-weight:700; padding:1px 6px; border-radius:var(--radius-full); border:2px solid var(--bg-primary); letter-spacing:.3px; }
        .day-cell.today .day-number { color:var(--galaxy-secondary); font-weight:800; }
        
        .day-cell.color-0 { background:#FF6B6B !important; }
        .day-cell.color-1 { background:#FF8E53 !important; }
        .day-cell.color-2 { background:#FECA57 !important; }
        .day-cell.color-3 { background:#48DBFB !important; }
        .day-cell.color-4 { background:#FF9FF3 !important; }
        .day-cell.color-5 { background:#54A0FF !important; }
        .day-cell.color-6 { background:#5F27CD !important; }
        .day-cell.color-7 { background:#1DD1A1 !important; }
        .day-cell.color-8 { background:#F368E0 !important; }
        .day-cell.color-9 { background:#00D2D3 !important; }
        .day-cell.color-0 .day-number,.day-cell.color-1 .day-number,.day-cell.color-2 .day-number,
        .day-cell.color-3 .day-number,.day-cell.color-4 .day-number,.day-cell.color-5 .day-number,
        .day-cell.color-6 .day-number,.day-cell.color-7 .day-number,.day-cell.color-8 .day-number,
        .day-cell.color-9 .day-number { color:#1A1A2E !important; text-shadow:none !important; }
        .day-cell.color-0 .employee-name,.day-cell.color-1 .employee-name,.day-cell.color-2 .employee-name,
        .day-cell.color-3 .employee-name,.day-cell.color-4 .employee-name,.day-cell.color-5 .employee-name,
        .day-cell.color-6 .employee-name,.day-cell.color-7 .employee-name,.day-cell.color-8 .employee-name,
        .day-cell.color-9 .employee-name { background:rgba(255,255,255,0.85) !important; border:1px solid rgba(0,0,0,0.08) !important; }
        .day-cell.color-0 .employee-name span,.day-cell.color-1 .employee-name span,
        .day-cell.color-2 .employee-name span,.day-cell.color-3 .employee-name span,
        .day-cell.color-4 .employee-name span,.day-cell.color-5 .employee-name span,
        .day-cell.color-6 .employee-name span,.day-cell.color-7 .employee-name span,
        .day-cell.color-8 .employee-name span,.day-cell.color-9 .employee-name span { color:#1A1A2E !important; }
        
        .color-palette { display:grid; grid-template-columns:repeat(5,1fr); gap:6px; margin:8px 0 12px; max-width:200px; }
        .color-option { width:100%; max-width:32px; aspect-ratio:1/1; border-radius:50%; border:3px solid var(--border-light); cursor:pointer; transition:all .15s; position:relative; margin:0 auto; }
        .color-option:hover { transform:scale(1.05); }
        .color-option.selected { border-color:var(--galaxy-secondary); transform:scale(1.1); border-width:3px; }
        .color-option .check-mark { position:absolute; top:50%; left:50%; transform:translate(-50%,-50%); color:white; font-size:9px; text-shadow:0 1px 2px rgba(0,0,0,0.3); display:none; }
        .color-option.selected .check-mark { display:block; }
        
        .search-bar { display:flex; align-items:center; background:var(--bg-secondary); border-radius:var(--radius-md); padding:0 14px; margin:8px 0 12px; border:2px solid var(--border-light); transition:all .2s; }
        .search-bar:focus-within { border-color:var(--galaxy-secondary); box-shadow:0 0 0 4px rgba(37,99,235,0.08); }
        .search-bar input { flex:1; border:none; background:transparent; padding:12px 6px; font-size:13px; color:var(--text-primary); outline:none; font-weight:500; }
        .search-bar input::placeholder { color:var(--text-quaternary); font-weight:400; }
        .search-clear { background:none; border:none; color:var(--text-tertiary); font-size:16px; cursor:pointer; padding:4px 8px; transition:color .2s; }
        .search-clear:hover { color:var(--text-primary); }
        
        .app-footer { margin-top:auto; padding:16px 0 4px; text-align:center; border-top:1px solid var(--border-light); font-size:10px; color:var(--text-tertiary); letter-spacing:1px; font-weight:300; }
        .app-footer .powered span { font-weight:600; color:var(--galaxy-primary); }
        
        .fab { position:fixed; bottom:24px; right:24px; z-index:40; display:none; flex-direction:column; align-items:center; gap:8px; }
        .fab.visible { display:flex; }
        .fab .fab-main { width:54px; height:54px; border-radius:50%; background:var(--gradient-primary); color:white; border:none; font-size:20px; cursor:pointer; box-shadow:0 4px 20px rgba(30,58,138,0.3); transition:transform .15s,box-shadow .2s; display:flex; align-items:center; justify-content:center; }
        .fab .fab-main:hover { box-shadow:0 6px 30px rgba(30,58,138,0.4); }
        .fab .fab-main:active { transform:scale(0.9); }
        .fab .fab-main.open { transform:rotate(45deg); }
        .fab .fab-actions { display:none; flex-direction:column; gap:8px; align-items:center; }
        .fab .fab-actions.open { display:flex; animation:fabSlideUp .2s ease; }
        @keyframes fabSlideUp { from { opacity:0; transform:translateY(16px); } to { opacity:1; transform:translateY(0); } }
        .fab .fab-actions button { width:44px; height:44px; border-radius:50%; border:none; background:var(--bg-primary); color:var(--text-secondary); font-size:16px; cursor:pointer; box-shadow:var(--shadow-md); transition:all .2s; display:flex; align-items:center; justify-content:center; }
        .fab .fab-actions button:hover { background:var(--bg-secondary); color:var(--text-primary); }
        .fab .fab-actions button:active { transform:scale(0.9); }
        
        .btn-top { position:fixed; bottom:96px; right:24px; z-index:39; width:48px; height:48px; border-radius:50%; background:var(--bg-primary); color:var(--text-secondary); border:2px solid var(--border-light); box-shadow:var(--shadow-md); cursor:pointer; transition:all .3s; display:none; align-items:center; justify-content:center; opacity:0; transform:scale(0.8); }
        .btn-top:hover { border-color:var(--galaxy-secondary); color:var(--galaxy-secondary); }
        .btn-top.visible { display:flex; opacity:1; transform:scale(1); }
        .btn-top:active { transform:scale(0.9); }
        .btn-top svg { width:20px; height:20px; }
        
        .modal { display:none; position:fixed; top:0; left:0; right:0; bottom:0; background:rgba(0,0,0,0.5); z-index:100; justify-content:center; align-items:center; padding:20px; backdrop-filter:blur(8px); animation:fadeIn .2s ease; }
        @keyframes fadeIn { from { opacity:0; } to { opacity:1; } }
        .modal.active { display:flex; }
        .modal-content { background:var(--bg-primary); border-radius:var(--radius-xl); padding:32px 28px; max-width:420px; width:100%; box-shadow:var(--shadow-xl); max-height:90vh; overflow-y:auto; transition:background .3s; animation:slideUp .25s ease; border:1px solid var(--border-light); }
        @keyframes slideUp { from { transform:translateY(20px); opacity:0; } to { transform:translateY(0); opacity:1; } }
        .modal-large { max-width:480px; }
        .modal-content h3 { font-size:20px; color:var(--text-primary); margin-bottom:16px; font-weight:700; }
        .modal-content .modal-date { font-size:13px; color:var(--text-tertiary); margin-bottom:16px; }
        .modal-content input,.modal-content select,.modal-content textarea { width:100%; padding:12px 16px; border:2px solid var(--border-light); border-radius:var(--radius-md); font-size:14px; outline:none; margin-bottom:12px; background:var(--bg-primary); color:var(--text-primary); transition:border-color .2s,box-shadow .2s; font-weight:500; }
        .modal-content input:focus,.modal-content select:focus,.modal-content textarea:focus { border-color:var(--galaxy-secondary); box-shadow:0 0 0 4px rgba(37,99,235,0.08); }
        .modal-content textarea { resize:vertical; min-height:80px; }
        .modal-actions { display:flex; gap:10px; margin-top:8px; }
        .modal-actions button { flex:1; padding:12px; border:none; border-radius:var(--radius-md); font-weight:600; font-size:13px; cursor:pointer; transition:all .2s; }
        .modal-actions button:active { transform:scale(0.96); }
        .btn-cancel { background:var(--bg-secondary); color:var(--text-secondary); }
        .btn-cancel:hover { background:var(--bg-tertiary); }
        .btn-save { background:var(--gradient-primary); color:white; }
        .btn-save:hover { background:linear-gradient(135deg,#15307A,#1D4ED8); }
        .btn-danger { background:var(--danger); color:white; }
        .btn-danger:hover { background:#DC2626; }
        
        .employee-form { display:grid; grid-template-columns:1fr 1fr; gap:8px; margin-bottom:12px; }
        .employee-form input { margin-bottom:0; }
        .employee-form .btn-save { grid-column:1/-1; }
        .employee-list { max-height:220px; overflow-y:auto; margin:8px 0; }
        .employee-item { display:flex; align-items:center; justify-content:space-between; padding:8px 12px; background:var(--bg-secondary); border-radius:var(--radius-sm); margin-bottom:4px; border:1px solid var(--border-light); transition:all .2s; }
        .employee-item:hover { border-color:var(--border-medium); }
        .employee-item .emp-info { flex:1; min-width:0; }
        .employee-item .emp-name { font-weight:600; font-size:13px; color:var(--text-secondary); }
        .employee-item .emp-color-preview { width:22px; height:22px; border-radius:50%; margin:0 8px; flex-shrink:0; border:2px solid var(--bg-primary); }
        .employee-item .emp-actions { display:flex; gap:4px; }
        .employee-item .emp-actions button { padding:2px 10px; border:none; border-radius:var(--radius-sm); font-size:11px; cursor:pointer; transition:all .15s; }
        .employee-item .emp-actions button:active { transform:scale(0.9); }
        .emp-edit { background:var(--galaxy-primary); color:white; }
        .emp-edit:hover { background:#15307A; }
        .emp-delete { background:var(--danger); color:white; }
        .emp-delete:hover { background:#DC2626; }
        
        .add-btn { padding:8px 16px; border:2px solid var(--border-light); border-radius:var(--radius-md); background:var(--bg-primary); color:var(--text-secondary); font-size:12px; font-weight:500; cursor:pointer; transition:all .2s; display:inline-flex; align-items:center; gap:6px; letter-spacing:.3px; }
        .add-btn:hover { border-color:var(--galaxy-secondary); color:var(--galaxy-secondary); background:rgba(37,99,235,0.04); }
        .add-btn:active { transform:scale(0.96); }
        .add-btn svg { width:14px; height:14px; }
        
        .client-list { max-height:220px; overflow-y:auto; margin:8px 0; }
        .client-item { display:flex; align-items:center; justify-content:space-between; padding:8px 12px; background:var(--bg-secondary); border-radius:var(--radius-sm); margin-bottom:4px; border:1px solid var(--border-light); transition:all .2s; }
        .client-item:hover { border-color:var(--border-medium); }
        .client-item .client-info { flex:1; min-width:0; }
        .client-item .client-name { font-weight:600; font-size:13px; color:var(--text-secondary); }
        .client-item .client-details { font-size:10px; color:var(--text-tertiary); font-style:italic; }
        .client-item .client-status { font-size:9px; font-weight:600; padding:2px 10px; border-radius:var(--radius-full); margin-left:8px; }
        .client-item .client-status.active { background:#DCFCE7; color:#166534; }
        .client-item .client-status.inactive { background:#FEE2E2; color:#991B1B; }
        .client-item .client-actions { display:flex; gap:4px; flex-shrink:0; }
        .client-item .client-actions button { padding:2px 10px; border:none; border-radius:var(--radius-sm); font-size:11px; cursor:pointer; transition:all .15s; }
        .client-item .client-actions button:active { transform:scale(0.9); }
        .client-edit { background:var(--galaxy-primary); color:white; }
        .client-edit:hover { background:#15307A; }
        .client-delete { background:var(--danger); color:white; }
        .client-delete:hover { background:#DC2626; }
        
        .settings-group { margin-bottom:18px; }
        .settings-group label { display:block; font-size:12px; font-weight:600; color:var(--text-secondary); margin-bottom:4px; }
        .settings-group input { width:100%; padding:12px 16px; border:2px solid var(--border-light); border-radius:var(--radius-md); font-size:14px; outline:none; background:var(--bg-primary); color:var(--text-primary); transition:border-color .2s,box-shadow .2s; font-weight:500; }
        .settings-group input:focus { border-color:var(--galaxy-secondary); box-shadow:0 0 0 4px rgba(37,99,235,0.08); }
        .settings-group .help-text { font-size:11px; color:var(--text-tertiary); margin-top:4px; }
        
        .detail-row { display:flex; padding:8px 0; border-bottom:1px solid var(--border-light); }
        .detail-label { font-weight:600; color:var(--text-tertiary); width:100px; flex-shrink:0; font-size:12px; text-transform:uppercase; letter-spacing:.5px; }
        .detail-value { flex:1; color:var(--text-primary); font-size:13px; word-break:break-word; font-weight:500; }
        .detail-value.italic { font-style:italic; }
        
        .toast-container { position:fixed; bottom:80px; left:50%; transform:translateX(-50%); z-index:200; width:90%; max-width:400px; }
        .toast { background:var(--text-primary); color:var(--bg-primary); padding:12px 20px; border-radius:var(--radius-md); box-shadow:var(--shadow-lg); font-weight:500; font-size:13px; text-align:center; animation:toastIn .3s ease; transition:background .3s,color .3s; }
        .toast.success { background:var(--success); color:white; }
        .toast.error { background:var(--danger); color:white; }
        .toast.info { background:var(--gradient-primary); color:white; }
        @keyframes toastIn { from { opacity:0; transform:translateY(16px); } to { opacity:1; transform:translateY(0); } }
        
        #loadingSpinner { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:rgba(0,0,0,0.4); z-index:9999; justify-content:center; align-items:center; backdrop-filter:blur(4px); }
        .spinner-circle { width:40px; height:40px; border:4px solid rgba(255,255,255,0.2); border-top-color:var(--galaxy-secondary); border-radius:50%; animation:spin 1s linear infinite; }
        @keyframes spin { to { transform:rotate(360deg); } }
        
        ::-webkit-scrollbar { width:4px; }
        ::-webkit-scrollbar-track { background:var(--bg-secondary); }
        ::-webkit-scrollbar-thumb { background:var(--text-quaternary); border-radius:10px; }
        ::-webkit-scrollbar-thumb:hover { background:var(--text-tertiary); }
        
        @media (max-width:400px) {
            .login-box { padding:24px 16px; }
            .day-cell { min-height:58px; }
            .day-cell .day-number { font-size:12px; }
            .day-cell .employee-name { font-size:7px; }
            .employee-form { grid-template-columns:1fr; }
            .color-palette { max-width:160px; gap:4px; }
            .color-option { max-width:26px; }
            .app-container { padding:0 12px 16px; }
            .share-buttons { flex-wrap:wrap; }
            .share-btn { min-width:80px; max-width:100%; flex:1; }
            .modal-content { padding:20px 16px; }
            .detail-label { width:80px; font-size:11px; }
            .detail-value { font-size:12px; }
            .client-info-row { flex-direction:column; }
            .client-info-label { width:100%; margin-bottom:2px; }
            .action-buttons { flex-direction:column; align-items:center; }
            .action-btn { max-width:100%; width:100%; }
            .day-name { font-size:9px; padding:8px 0 6px; }
            #splashScreen .splash-logo { font-size:32px; }
            .app-title .main { font-size:16px; letter-spacing:3px; }
            .app-title .sub { font-size:7px; letter-spacing:2px; }
            .app-header { padding:10px 0 8px; margin:0 -12px; padding-left:12px; padding-right:12px; }
            .brand-area { margin:0 -12px; padding-left:12px; padding-right:12px; }
        }
        @media (max-width:350px) {
            .share-btn { font-size:10px; padding:8px 12px; }
            .share-btn span { font-size:10px; }
            .calendar-grid { gap:4px; }
            .day-cell { min-height:48px; padding:4px 2px; }
        }
        @media (min-width:481px) {
            .app-container { border-radius:var(--radius-xl); margin:20px auto; min-height:calc(100vh - 40px); box-shadow:var(--shadow-lg); }
            .fab { right:calc(50% - 240px + 24px); }
            .btn-top { right:calc(50% - 240px + 24px); }
        }
    </style>
</head>
<body>

<!-- ===== SPLASH ===== -->
<div id="splashScreen">
    <div class="splash-logo">PLANT<span>ONIX</span></div>
    <div class="splash-subtitle">Sistema de Gestão de Escalas</div>
    <div class="splash-loader"><div class="splash-loader-bar"></div></div>
    <div class="splash-version">v16.0</div>
</div>

<!-- ===== LOADING ===== -->
<div id="loadingSpinner"><div class="spinner-circle"></div></div>

<!-- ===== TOAST ===== -->
<div class="toast-container" id="toastContainer"></div>

<!-- ===== MENU OVERLAY ===== -->
<div class="menu-overlay" id="menuOverlay"></div>

<!-- ===== SIDEBAR ===== -->
<nav class="side-menu" id="sideMenu">
    <div class="side-menu-header">
        <button class="side-menu-close" id="sideMenuClose">✕</button>
        <div class="menu-brand">PLANT<span>ONIX</span></div>
        <div class="menu-sub" id="menuCompanyName">Sistema de Gestão de Escalas</div>
    </div>
    <div class="side-menu-items">
        <button class="side-menu-item active" data-page="home"><span class="menu-icon"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M3 12l2-2m0 0l7-7 7 7M5 10v10a1 1 0 001 1h3m10-11l2 2m-2-2v10a1 1 0 01-1 1h-3m-6 0a1 1 0 001-1v-4a1 1 0 011-1h2a1 1 0 011 1v4a1 1 0 001 1"/></svg></span>Início</button>
        <button class="side-menu-item" data-page="calendar"><span class="menu-icon"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="3" y="4" width="18" height="18" rx="2" ry="2"/><line x1="16" y1="2" x2="16" y2="6"/><line x1="8" y1="2" x2="8" y2="6"/><line x1="3" y1="10" x2="21" y2="10"/></svg></span>Escalas</button>
        <button class="side-menu-item" data-page="clients"><span class="menu-icon"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2"/><circle cx="12" cy="7" r="4"/></svg></span>Clientes</button>
        <button class="side-menu-item" data-page="employees"><span class="menu-icon"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M17 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2"/><circle cx="9" cy="7" r="4"/><path d="M23 21v-2a4 4 0 0 0-3-3.87"/><path d="M16 3.13a4 4 0 0 1 0 7.75"/></svg></span>Colaboradores</button>
        <button class="side-menu-item" data-page="export"><span class="menu-icon"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"/><polyline points="7 10 12 15 17 10"/><line x1="12" y1="15" x2="12" y2="3"/></svg></span>Exportar</button>
        <button class="side-menu-item" data-page="settings"><span class="menu-icon"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="3"/><path d="M19.4 15a1.65 1.65 0 0 0 .33 1.82l.06.06a2 2 0 0 1 0 2.83 2 2 0 0 1-2.83 0l-.06-.06a1.65 1.65 0 0 0-1.82-.33 1.65 1.65 0 0 0-1 1.51V21a2 2 0 0 1-2 2 2 2 0 0 1-2-2v-.09A1.65 1.65 0 0 0 9 19.4a1.65 1.65 0 0 0-1.82.33l-.06.06a2 2 0 0 1-2.83 0 2 2 0 0 1 0-2.83l.06-.06A1.65 1.65 0 0 0 4.68 15a1.65 1.65 0 0 0-1.51-1H3a2 2 0 0 1-2-2 2 2 0 0 1 2-2h.09A1.65 1.65 0 0 0 4.6 9a1.65 1.65 0 0 0-.33-1.82l-.06-.06a2 2 0 0 1 0-2.83 2 2 0 0 1 2.83 0l.06.06A1.65 1.65 0 0 0 9 4.68a1.65 1.65 0 0 0 1-1.51V3a2 2 0 0 1 2-2 2 2 0 0 1 2 2v.09a1.65 1.65 0 0 0 1 1.51 1.65 1.65 0 0 0 1.82-.33l.06-.06a2 2 0 0 1 2.83 0 2 2 0 0 1 0 2.83l-.06.06A1.65 1.65 0 0 0 19.4 9a1.65 1.65 0 0 0 1.51 1H21a2 2 0 0 1 2 2 2 2 0 0 1-2 2h-.09a1.65 1.65 0 0 0-1.51 1z"/></svg></span>Configurações</button>
        <button class="side-menu-item" data-page="about"><span class="menu-icon"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="10"/><line x1="12" y1="16" x2="12" y2="12"/><line x1="12" y1="8" x2="12.01" y2="8"/></svg></span>Sobre</button>
        <div class="side-menu-divider"></div>
        <button class="side-menu-item" data-page="logout"><span class="menu-icon"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M9 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h4"/><polyline points="16 17 21 12 16 7"/><line x1="21" y1="12" x2="9" y2="12"/></svg></span>Sair</button>
    </div>
    <div class="side-menu-footer"><div class="powered">Tecnologia <span>PLANTONIX</span></div></div>
</nav>

<!-- ===== LOGIN ===== -->
<div class="login-screen active" id="loginScreen">
    <div class="login-box">
        <div class="logo"><h1>PLANT<span>ONIX</span></h1><p>Gestão de Escalas</p></div>
        <div id="loginForm">
            <div class="form-group"><label>E-mail</label><input type="email" id="loginEmail" placeholder="seu@email.com" value="admin@plantonix.com"></div>
            <div class="form-group">
                <label>Senha</label>
                <div class="password-wrapper">
                    <input type="password" id="loginPassword" placeholder="Digite sua senha" value="123456">
                    <button type="button" class="password-toggle" id="passwordToggle"><svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"/><circle cx="12" cy="12" r="3"/></svg></button>
                </div>
            </div>
            <button class="btn-login" id="loginBtn">Entrar</button>
            <div id="loginError" class="login-error"></div>
        </div>
        <div class="login-footer"><span>Acesse com suas credenciais</span></div>
    </div>
</div>

<!-- ===== APP ===== -->
<div class="app-container" id="app">
    <header class="app-header">
        <button class="menu-icon" id="menuIcon"><svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><line x1="3" y1="6" x2="21" y2="6"/><line x1="3" y1="12" x2="21" y2="12"/><line x1="3" y1="18" x2="21" y2="18"/></svg></button>
        <div class="app-title"><span class="main">PLANT<span>ONIX</span></span><span class="sub" id="headerSubname">Sistema de Gestão de Escalas</span></div>
        <div class="header-actions">
            <button class="logout-btn" id="headerLogout"><svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M9 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h4"/><polyline points="16 17 21 12 16 7"/><line x1="21" y1="12" x2="9" y2="12"/></svg></button>
            <button id="themeToggle" class="theme-toggle"><svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M21 12.79A9 9 0 1 1 11.21 3 7 7 0 0 0 21 12.79z"/></svg></button>
        </div>
    </header>

    <div class="brand-area">
        <div class="brand-name" id="brandCompanyName">Minha Empresa</div>
        <div class="brand-subname" id="brandSubname"><span id="brandSubnameText">Clique para definir a empresa</span><span class="edit-icon">✎</span></div>
        <div class="brand-powered">Tecnologia <span>PLANTONIX</span></div>
    </div>

    <div class="action-buttons">
        <button class="action-btn" id="openClientsBtn"><svg class="svg-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2"/><circle cx="12" cy="7" r="4"/><path d="M12 3v8"/><path d="M8 7h8"/></svg>Clientes</button>
        <button class="action-btn" id="openEmployeesBtn"><svg class="svg-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M17 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2"/><circle cx="9" cy="7" r="4"/><path d="M23 21v-2a4 4 0 0 0-3-3.87"/><path d="M16 3.13a4 4 0 0 1 0 7.75"/></svg>Colaboradores</button>
    </div>

    <div class="share-buttons">
        <button class="share-btn pdf" id="downloadPDFBtn"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/><polyline points="14 2 14 8 20 8"/><line x1="16" y1="13" x2="8" y2="13"/><line x1="16" y1="17" x2="8" y2="17"/></svg><span>Baixar PDF</span></button>
        <button class="share-btn png" id="downloadPNGBtn"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="2" y="2" width="20" height="20" rx="2.18"/><line x1="8" y1="2" x2="8" y2="22"/><line x1="16" y1="2" x2="16" y2="22"/><line x1="2" y1="8" x2="22" y2="8"/><line x1="2" y1="16" x2="22" y2="16"/></svg><span>Baixar PNG</span></button>
        <button class="share-btn whatsapp" id="shareWhatsAppBtn"><svg viewBox="0 0 24 24" fill="currentColor"><path d="M17.472 14.382c-.297-.149-1.758-.867-2.03-.967-.273-.099-.471-.148-.67.15-.197.297-.767.966-.94 1.164-.173.199-.347.223-.644.075-.297-.15-1.255-.463-2.39-1.475-.883-.788-1.48-1.761-1.653-2.059-.173-.297-.018-.458.13-.606.134-.133.298-.347.446-.52.149-.174.198-.298.298-.497.099-.198.05-.371-.025-.52-.075-.149-.669-1.612-.916-2.207-.242-.579-.487-.5-.669-.51-.173-.008-.371-.01-.57-.01-.198 0-.52.074-.792.372-.272.297-1.04 1.016-1.04 2.479 0 1.462 1.065 2.875 1.213 3.074.149.198 2.096 3.2 5.077 4.487.709.306 1.262.489 1.694.625.712.227 1.36.195 1.871.118.571-.085 1.758-.719 2.006-1.413.248-.694.248-1.289.173-1.413-.074-.124-.272-.198-.57-.347m-5.421 7.403h-.004a9.87 9.87 0 01-5.031-1.378l-.361-.214-3.741.982.998-3.648-.235-.374a9.86 9.86 0 01-1.51-5.26c.001-5.45 4.436-9.884 9.888-9.884 2.64 0 5.122 1.03 6.988 2.898a9.825 9.825 0 012.893 6.994c-.003 5.45-4.437 9.884-9.885 9.884m8.413-18.297A11.815 11.815 0 0012.05 0C5.495 0 .16 5.335.157 11.892c0 2.096.547 4.142 1.588 5.945L.057 24l6.305-1.654a11.882 11.882 0 005.683 1.448h.005c6.554 0 11.89-5.335 11.893-11.893a11.821 11.821 0 00-3.48-8.413z"/></svg><span>Compartilhar</span></button>
    </div>

    <div class="client-selector"><label>Cliente Atual</label><select id="clientSelector"><option value="">Selecione um cliente</option></select></div>

    <div class="client-info-card" id="clientInfoCard">
        <div class="client-info-row"><span class="client-info-label">Cliente:</span><span class="client-info-value placeholder" id="infoClient">Nenhum selecionado</span></div>
        <div class="client-info-row"><span class="client-info-label">Endereço:</span><span class="client-info-value placeholder italic" id="infoAddress">---</span></div>
        <div class="client-info-row"><span class="client-info-label">Observação:</span><span class="client-info-value placeholder italic" id="infoObservation">Nenhuma observação cadastrada</span></div>
    </div>

    <div class="stats-grid">
        <div class="stat-card"><div class="stat-number" id="shiftCount">0</div><div class="stat-label">Plantões</div></div>
        <div class="stat-card"><div class="stat-number" id="employeeCount">0</div><div class="stat-label">Colaboradores</div></div>
        <div class="stat-card"><div class="stat-number" id="currentMonthLabel">Junho</div><div class="stat-label">Mês</div></div>
    </div>

    <div class="calendar-header">
        <button class="calendar-nav" id="prevMonthBtn"><svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="15 18 9 12 15 6"/></svg></button>
        <span class="calendar-title" id="monthLabel">Junho 2026</span>
        <button class="calendar-nav" id="nextMonthBtn"><svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="9 18 15 12 9 6"/></svg></button>
    </div>
    <div id="calendarCapture"><div class="calendar-grid" id="calendarGrid"><div class="day-name">Dom</div><div class="day-name">Seg</div><div class="day-name">Ter</div><div class="day-name">Qua</div><div class="day-name">Qui</div><div class="day-name">Sex</div><div class="day-name">Sáb</div></div></div>

    <div class="search-bar">
        <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="flex-shrink:0;color:var(--text-quaternary);"><circle cx="11" cy="11" r="8"/><line x1="21" y1="21" x2="16.65" y2="16.65"/></svg>
        <input type="text" id="searchInput" placeholder="Buscar colaborador...">
        <button id="searchClear" class="search-clear" style="display:none;">✕</button>
    </div>

    <footer class="app-footer"><div class="powered">Tecnologia <span>PLANTONIX</span></div></footer>
</div>

<!-- ===== TOP BUTTON ===== -->
<button class="btn-top" id="btnTop"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="18 15 12 9 6 15"/></svg></button>

<!-- ===== FAB ===== -->
<div class="fab" id="fab">
    <div class="fab-actions" id="fabActions">
        <button id="fabNewShift"><svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="10"/><line x1="12" y1="8" x2="12" y2="16"/><line x1="8" y1="12" x2="16" y2="12"/></svg></button>
        <button id="fabShare"><svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="18" cy="5" r="3"/><circle cx="6" cy="12" r="3"/><circle cx="18" cy="19" r="3"/><line x1="8.59" y1="13.51" x2="15.42" y2="17.49"/><line x1="15.41" y1="6.51" x2="8.59" y2="10.49"/></svg></button>
    </div>
    <button class="fab-main" id="fabMain"><svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><line x1="12" y1="5" x2="12" y2="19"/><line x1="5" y1="12" x2="19" y2="12"/></svg></button>
</div>

<!-- ===== MODAIS ===== -->
<!-- Edit -->
<div class="modal" id="editModal"><div class="modal-content"><h3 id="editModalTitle">Novo Plantão</h3><div class="modal-date" id="modalDate">Dia 1 de Janeiro</div><select id="modalClientSelect"><option value="">Selecione o cliente *</option></select><select id="modalEmployeeSelect"><option value="">Selecione o colaborador</option></select><input type="text" id="modalNameInput" placeholder="Ou digite um novo nome" maxlength="30"><div class="modal-actions"><button class="btn-cancel" id="editModalCancel">Cancelar</button><button class="btn-save" id="editModalSave">Salvar</button></div></div></div>

<!-- View -->
<div class="modal" id="viewModal"><div class="modal-content"><h3>Detalhes do Plantão</h3><div id="viewDetails"><div class="detail-row"><span class="detail-label">Cliente:</span><span class="detail-value" id="viewClient">-</span></div><div class="detail-row"><span class="detail-label">Profissional:</span><span class="detail-value" id="viewProfessional">-</span></div><div class="detail-row"><span class="detail-label">Data:</span><span class="detail-value" id="viewDate">-</span></div></div><div class="modal-actions"><button class="btn-cancel" id="viewModalClose">Fechar</button><button class="btn-save" id="viewModalEdit">Editar</button><button class="btn-danger" id="viewModalDelete">Excluir</button></div></div></div>

<!-- Clients -->
<div class="modal" id="clientsModal"><div class="modal-content modal-large"><h3>Clientes</h3><div class="employee-form"><input type="text" id="clientName" placeholder="Nome *" maxlength="80"><input type="text" id="clientAddress" placeholder="Endereço" maxlength="200"><textarea id="clientObservation" placeholder="Observações" maxlength="500" rows="2"></textarea><select id="clientStatus"><option value="ativo">Ativo</option><option value="inativo">Inativo</option></select><button class="add-btn" id="clientAddBtn" style="grid-column:1/-1;justify-content:center;width:100%;"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><line x1="12" y1="5" x2="12" y2="19"/><line x1="5" y1="12" x2="19" y2="12"/></svg>Adicionar Cliente</button></div><div class="client-list" id="clientList"></div><div class="modal-actions"><button class="btn-cancel" id="clientsModalClose">Fechar</button></div></div></div>

<!-- Employees -->
<div class="modal" id="employeesModal"><div class="modal-content modal-large"><h3>Colaboradores</h3><div class="employee-form"><input type="text" id="empName" placeholder="Nome" maxlength="30"><button class="add-btn" id="empAddBtn" style="grid-column:1/-1;justify-content:center;width:100%;"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><line x1="12" y1="5" x2="12" y2="19"/><line x1="5" y1="12" x2="19" y2="12"/></svg>Adicionar Colaborador</button></div><p style="font-size:11px;color:var(--text-tertiary);margin-bottom:6px;">Cor:</p><div class="color-palette" id="colorPalette"></div><div class="employee-list" id="employeeList"></div><div class="modal-actions"><button class="btn-cancel" id="employeesModalClose">Fechar</button></div></div></div>

<!-- Settings -->
<div class="modal" id="settingsModal"><div class="modal-content"><h3>Configurações</h3><p style="font-size:13px;color:var(--text-tertiary);margin-bottom:16px;">Personalize a identidade da sua empresa</p><div class="settings-group"><label>Nome da Empresa</label><input type="text" id="settingsCompanyName" placeholder="Ex: Minha Empresa"></div><div class="settings-group"><label>Subtítulo</label><input type="text" id="settingsCompanySubtitle" placeholder="Ex: Gestão de Escalas"><div class="help-text">Aparece abaixo do nome da empresa</div></div><div class="modal-actions"><button class="btn-cancel" id="settingsModalCancel">Cancelar</button><button class="btn-save" id="settingsModalSave">Salvar</button></div></div></div>

<!-- About -->
<div class="modal" id="aboutModal"><div class="modal-content"><h3 style="text-align:center;font-weight:300;letter-spacing:4px;font-size:24px;">PLANT<span style="font-weight:700;color:var(--galaxy-primary);">ONIX</span></h3><div style="text-align:center;margin-top:8px;font-size:14px;color:var(--text-tertiary);font-weight:300;letter-spacing:1px;">Versão 16.0</div><div style="text-align:center;margin-top:12px;padding-top:16px;border-top:1px solid var(--border-light);font-size:12px;color:var(--galaxy-primary);font-weight:500;letter-spacing:1px;">TECNOLOGIA PARA ESCALAS</div><div style="text-align:center;margin-top:4px;font-size:11px;color:var(--text-quaternary);letter-spacing:1px;font-weight:300;">Sistema Inteligente de Gestão de Escalas</div><div style="text-align:center;margin-top:12px;font-size:10px;color:var(--text-quaternary);letter-spacing:0.5px;font-weight:300;">© 2026 PLANTONIX - Todos os direitos reservados</div><div class="modal-actions" style="margin-top:16px;"><button class="btn-cancel" id="aboutModalClose">Fechar</button></div></div></div>

<script>
// ============================================
// PLANTONIX v16.0 - COMPLETO
// ============================================

// ===== STATE =====
const state = {
    isLoggedIn: false,
    employees: [],
    clients: [],
    scaleData: {},
    history: [{ action: 'Sistema iniciado', data: new Date().toLocaleString() }],
    currentYear: new Date().getFullYear(),
    currentMonth: new Date().getMonth(),
    theme: localStorage.getItem('plantonix_theme') || 'light',
    searchTerm: '',
    selectedColor: 0,
    editingDate: null,
    companyName: 'Minha Empresa',
    companySubtitle: 'Sistema de Gestão de Escalas',
    selectedClientId: null,
    viewingDate: null
};

const MONTHS = ['Janeiro','Fevereiro','Março','Abril','Maio','Junho','Julho','Agosto','Setembro','Outubro','Novembro','Dezembro'];
const COLORS = ['#FF6B6B','#FF8E53','#FECA57','#48DBFB','#FF9FF3','#54A0FF','#5F27CD','#1DD1A1','#F368E0','#00D2D3'];

// ===== UTILS =====
function formatDate(y,m,d) { return y+'-'+String(m+1).padStart(2,'0')+'-'+String(d).padStart(2,'0'); }
function getMonthName(m) { return MONTHS[m] || 'Mês'; }
function getDaysInMonth(y,m) { return new Date(y,m+1,0).getDate(); }
function getFirstDayOfMonth(y,m) { return new Date(y,m,1).getDay(); }
function generateId() { return 'id_'+Date.now()+'_'+Math.random().toString(36).substring(2,6); }

function showToast(message, type) {
    type = type || 'info';
    const container = document.querySelector('.toast-container') || (() => { const el = document.createElement('div'); el.className = 'toast-container'; document.body.appendChild(el); return el; })();
    const toast = document.createElement('div');
    toast.className = 'toast ' + type;
    toast.textContent = message;
    container.innerHTML = '';
    container.appendChild(toast);
    setTimeout(() => { toast.style.opacity = '0'; toast.style.transform = 'translateY(20px)'; setTimeout(() => { if (container.children.length === 0) container.remove(); }, 300); }, 3000);
}

function showLoading(show) { document.getElementById('loadingSpinner').style.display = show ? 'flex' : 'none'; }

function toggleMenu() {
    document.getElementById('sideMenu').classList.toggle('open');
    document.getElementById('menuOverlay').classList.toggle('active');
    document.body.style.overflow = document.getElementById('sideMenu').classList.contains('open') ? 'hidden' : '';
}

function closeMenu() {
    document.getElementById('sideMenu').classList.remove('open');
    document.getElementById('menuOverlay').classList.remove('active');
    document.body.style.overflow = '';
}

function openModal(id) { document.getElementById(id).classList.add('active'); }
function closeModal(id) { document.getElementById(id).classList.remove('active'); }
function scrollToTop() { window.scrollTo({ top: 0, behavior: 'smooth' }); }

// ===== THEME =====
function applyTheme() {
    document.documentElement.setAttribute('data-theme', state.theme);
    const toggle = document.getElementById('themeToggle');
    if (state.theme === 'light') {
        toggle.innerHTML = '<svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M21 12.79A9 9 0 1 1 11.21 3 7 7 0 0 0 21 12.79z"/></svg>';
    } else {
        toggle.innerHTML = '<svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="5"/><line x1="12" y1="1" x2="12" y2="3"/><line x1="12" y1="21" x2="12" y2="23"/><line x1="4.22" y1="4.22" x2="5.64" y2="5.64"/><line x1="18.36" y1="18.36" x2="19.78" y2="19.78"/><line x1="1" y1="12" x2="3" y2="12"/><line x1="21" y1="12" x2="23" y2="12"/><line x1="4.22" y1="19.78" x2="5.64" y2="18.36"/><line x1="18.36" y1="5.64" x2="19.78" y2="4.22"/></svg>';
    }
}

function toggleTheme() {
    state.theme = state.theme === 'light' ? 'dark' : 'light';
    localStorage.setItem('plantonix_theme', state.theme);
    applyTheme();
}

// ===== COMPANY SETTINGS =====
function loadCompanySettings() {
    const name = localStorage.getItem('plantonix_company_name');
    const subtitle = localStorage.getItem('plantonix_company_subtitle');
    if (name) state.companyName = name;
    if (subtitle) state.companySubtitle = subtitle;
}

function saveCompanySettings(name, subtitle) {
    if (name) { state.companyName = name; localStorage.setItem('plantonix_company_name', name); }
    if (subtitle !== undefined) { state.companySubtitle = subtitle || 'Sistema de Gestão de Escalas'; localStorage.setItem('plantonix_company_subtitle', state.companySubtitle); }
    updateCompanyNameDisplay();
    showToast('Configurações salvas!', 'success');
}

function updateCompanyNameDisplay() {
    const name = state.companyName || 'Minha Empresa';
    const subtitle = state.companySubtitle || 'Sistema de Gestão de Escalas';
    document.getElementById('brandCompanyName').textContent = name;
    document.getElementById('brandSubnameText').textContent = subtitle;
    document.getElementById('menuCompanyName').textContent = subtitle;
    document.getElementById('headerSubname').textContent = subtitle;
}

// ===== AUTH =====
function handleLogin() {
    state.isLoggedIn = true;
    document.getElementById('loginScreen').classList.remove('active');
    document.getElementById('app').classList.add('active');
    loadCompanySettings();
    updateCompanyNameDisplay();
    renderApp();
    showToast('Bem-vindo, Administrador!', 'success');
}

function handleLogout() {
    state.isLoggedIn = false;
    document.getElementById('app').classList.remove('active');
    document.getElementById('loginScreen').classList.add('active');
    closeMenu();
    showToast('Até logo!', 'info');
}

function togglePassword(inputId, button) {
    const input = document.getElementById(inputId);
    if (input.type === 'password') {
        input.type = 'text';
        button.innerHTML = '<svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M17.94 17.94A10.07 10.07 0 0 1 12 20c-7 0-11-8-11-8a18.45 18.45 0 0 1 5.06-5.94M9.9 4.24A9.12 9.12 0 0 1 12 4c7 0 11 8 11 8a18.5 18.5 0 0 1-2.16 3.19m-6.72-1.07a3 3 0 1 1-4.24-4.24"/><line x1="1" y1="1" x2="23" y2="23"/></svg>';
    } else {
        input.type = 'password';
        button.innerHTML = '<svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"/><circle cx="12" cy="12" r="3"/></svg>';
    }
}

// ===== CLIENTS =====
function renderClientSelector() {
    const select = document.getElementById('clientSelector');
    if (!select) return;
    let html = '<option value="">Selecione um cliente</option>';
    const active = state.clients.filter(c => c.status === 'ativo');
    const inactive = state.clients.filter(c => c.status === 'inativo');
    const sorted = active.concat(inactive);
    sorted.forEach(c => {
        const statusText = c.status === 'inativo' ? ' (Inativo)' : '';
        const selected = (state.selectedClientId === c.id) ? 'selected' : '';
        html += `<option value="${c.id}" ${selected}>${c.nome}${statusText}</option>`;
    });
    select.innerHTML = html;
    if (!state.selectedClientId && active.length > 0) {
        state.selectedClientId = active[0].id;
        select.value = state.selectedClientId;
    }
    renderClientInfo();
    renderCalendar();
}

function renderClientInfo() {
    const client = state.clients.find(c => c.id === state.selectedClientId);
    const nameEl = document.getElementById('infoClient');
    const addressEl = document.getElementById('infoAddress');
    const obsEl = document.getElementById('infoObservation');
    if (client) {
        nameEl.textContent = client.nome;
        nameEl.className = 'client-info-value';
        addressEl.textContent = client.endereco || '---';
        addressEl.className = 'client-info-value italic';
        obsEl.textContent = client.observacao || 'Nenhuma observação cadastrada';
        obsEl.className = 'client-info-value italic';
    } else {
        nameEl.textContent = 'Nenhum selecionado';
        nameEl.className = 'client-info-value placeholder';
        addressEl.textContent = '---';
        addressEl.className = 'client-info-value placeholder italic';
        obsEl.textContent = 'Nenhuma observação cadastrada';
        obsEl.className = 'client-info-value placeholder italic';
    }
}

function selectClient() {
    state.selectedClientId = document.getElementById('clientSelector').value || null;
    renderClientInfo();
    renderCalendar();
    updateStats();
}

function openClientsModal() {
    document.getElementById('clientName').value = '';
    document.getElementById('clientAddress').value = '';
    document.getElementById('clientObservation').value = '';
    document.getElementById('clientStatus').value = 'ativo';
    const addBtn = document.getElementById('clientAddBtn');
    addBtn.textContent = 'Adicionar Cliente';
    delete addBtn.dataset.editId;
    renderClientList();
    openModal('clientsModal');
}

function renderClientList() {
    const list = document.getElementById('clientList');
    if (!list) return;
    if (state.clients.length === 0) {
        list.innerHTML = '<p style="text-align:center;color:var(--text-tertiary);padding:20px;">Nenhum cliente cadastrado</p>';
        return;
    }
    let html = '';
    state.clients.forEach(c => {
        const statusClass = c.status === 'ativo' ? 'active' : 'inactive';
        const statusText = c.status === 'ativo' ? 'Ativo' : 'Inativo';
        html += `<div class="client-item"><div class="client-info"><span class="client-name">${c.nome}</span><span class="client-details">${c.endereco || ''}</span></div><span class="client-status ${statusClass}">${statusText}</span><div class="client-actions"><button class="client-edit" onclick="editClient('${c.id}')">✎</button><button class="client-delete" onclick="deleteClient('${c.id}')">✕</button></div></div>`;
    });
    list.innerHTML = html;
}

function addClient() {
    const nome = document.getElementById('clientName').value.trim();
    const endereco = document.getElementById('clientAddress').value.trim();
    const observacao = document.getElementById('clientObservation').value.trim();
    const status = document.getElementById('clientStatus').value;
    if (!nome) { showToast('Digite o nome do cliente.', 'error'); return; }
    state.clients.push({ id: generateId(), nome, endereco, observacao, status, criadoEm: new Date().toISOString() });
    state.history.unshift({ action: 'Adicionou cliente: ' + nome, data: new Date().toLocaleString() });
    document.getElementById('clientName').value = '';
    document.getElementById('clientAddress').value = '';
    document.getElementById('clientObservation').value = '';
    renderClientList();
    renderClientSelector();
    renderClientInfo();
    renderHistory();
    showToast('Cliente adicionado!', 'success');
}

function editClient(id) {
    const client = state.clients.find(c => c.id === id);
    if (!client) return;
    document.getElementById('clientName').value = client.nome;
    document.getElementById('clientAddress').value = client.endereco || '';
    document.getElementById('clientObservation').value = client.observacao || '';
    document.getElementById('clientStatus').value = client.status;
    const addBtn = document.getElementById('clientAddBtn');
    addBtn.textContent = 'Atualizar';
    addBtn.dataset.editId = id;
}

function updateClient(id) {
    const nome = document.getElementById('clientName').value.trim();
    const endereco = document.getElementById('clientAddress').value.trim();
    const observacao = document.getElementById('clientObservation').value.trim();
    const status = document.getElementById('clientStatus').value;
    if (!nome) { showToast('Digite o nome do cliente.', 'error'); return; }
    const client = state.clients.find(c => c.id === id);
    if (!client) return;
    const oldNome = client.nome;
    client.nome = nome; client.endereco = endereco; client.observacao = observacao; client.status = status;
    state.history.unshift({ action: 'Atualizou cliente: ' + oldNome + ' → ' + nome, data: new Date().toLocaleString() });
    const addBtn = document.getElementById('clientAddBtn');
    addBtn.textContent = 'Adicionar Cliente';
    delete addBtn.dataset.editId;
    document.getElementById('clientName').value = '';
    document.getElementById('clientAddress').value = '';
    document.getElementById('clientObservation').value = '';
    renderClientList();
    renderClientSelector();
    renderClientInfo();
    renderHistory();
    showToast('Cliente atualizado!', 'success');
}

function deleteClient(id) {
    if (!confirm('Tem certeza que deseja excluir este cliente?')) return;
    const client = state.clients.find(c => c.id === id);
    if (!client) return;
    state.clients = state.clients.filter(c => c.id !== id);
    if (state.selectedClientId === id) state.selectedClientId = null;
    state.history.unshift({ action: 'Removeu cliente: ' + client.nome, data: new Date().toLocaleString() });
    renderClientList();
    renderClientSelector();
    renderClientInfo();
    renderCalendar();
    renderHistory();
    showToast('Cliente removido.', 'info');
}

// ===== EMPLOYEES =====
function renderEmployeeList() {
    const list = document.getElementById('employeeList');
    if (!list) return;
    if (state.employees.length === 0) {
        list.innerHTML = '<p style="text-align:center;color:var(--text-tertiary);padding:20px;">Nenhum colaborador</p>';
        return;
    }
    let html = '';
    state.employees.forEach(emp => {
        const color = COLORS[emp.cor] || '#cccccc';
        html += `<div class="employee-item"><div class="emp-info"><span class="emp-name">${emp.nome}</span></div><div class="emp-color-preview" style="background:${color};"></div><div class="emp-actions"><button class="emp-edit" onclick="editEmployee('${emp.id}')">✎</button><button class="emp-delete" onclick="deleteEmployee('${emp.id}')">✕</button></div></div>`;
    });
    list.innerHTML = html;
}

function openEmployeesModal() {
    document.getElementById('empName').value = '';
    const addBtn = document.getElementById('empAddBtn');
    addBtn.textContent = 'Adicionar Colaborador';
    delete addBtn.dataset.editId;
    renderColorPalette();
    renderEmployeeList();
    openModal('employeesModal');
}

function addEmployee() {
    const nome = document.getElementById('empName').value.trim();
    const selectedColor = parseInt(document.getElementById('empAddBtn').dataset.selectedColor) || 0;
    if (!nome) { showToast('Digite o nome.', 'error'); return; }
    state.employees.push({ id: generateId(), nome, cor: selectedColor });
    state.history.unshift({ action: 'Adicionou colaborador: ' + nome, data: new Date().toLocaleString() });
    renderEmployeeList();
    renderCalendar();
    renderHistory();
    document.getElementById('empName').value = '';
    showToast(nome + ' adicionado!', 'success');
}

function editEmployee(id) {
    const emp = state.employees.find(e => e.id === id);
    if (!emp) return;
    document.getElementById('empName').value = emp.nome;
    const selectedColor = emp.cor || 0;
    document.getElementById('empAddBtn').dataset.selectedColor = selectedColor;
    const palette = document.getElementById('colorPalette');
    const options = palette.querySelectorAll('.color-option');
    options.forEach(opt => {
        const idx = parseInt(opt.dataset.colorIndex);
        opt.classList.toggle('selected', idx === selectedColor);
    });
    const addBtn = document.getElementById('empAddBtn');
    addBtn.textContent = 'Atualizar';
    addBtn.dataset.editId = id;
}

function updateEmployee(id) {
    const nome = document.getElementById('empName').value.trim();
    const cor = parseInt(document.getElementById('empAddBtn').dataset.selectedColor) || 0;
    if (!nome) { showToast('Digite o nome.', 'error'); return; }
    const emp = state.employees.find(e => e.id === id);
    if (!emp) return;
    const oldNome = emp.nome;
    emp.nome = nome; emp.cor = cor;
    for (const key in state.scaleData) {
        if (state.scaleData[key].nome === oldNome) {
            state.scaleData[key].nome = nome;
            state.scaleData[key].cor = cor;
        }
    }
    state.history.unshift({ action: 'Atualizou colaborador: ' + oldNome + ' → ' + nome, data: new Date().toLocaleString() });
    renderEmployeeList();
    renderCalendar();
    renderHistory();
    const addBtn = document.getElementById('empAddBtn');
    addBtn.textContent = 'Adicionar Colaborador';
    delete addBtn.dataset.editId;
    document.getElementById('empName').value = '';
    showToast('Atualizado!', 'success');
}

function deleteEmployee(id) {
    if (!confirm('Tem certeza que deseja excluir este colaborador?')) return;
    const emp = state.employees.find(e => e.id === id);
    if (!emp) return;
    state.employees = state.employees.filter(e => e.id !== id);
    for (const key in state.scaleData) {
        if (state.scaleData[key].nome === emp.nome) delete state.scaleData[key];
    }
    state.history.unshift({ action: 'Removeu colaborador: ' + emp.nome, data: new Date().toLocaleString() });
    renderEmployeeList();
    renderCalendar();
    renderHistory();
    showToast('Colaborador removido.', 'info');
}

function renderColorPalette() {
    const palette = document.getElementById('colorPalette');
    if (!palette) return;
    const selectedColor = parseInt(document.getElementById('empAddBtn').dataset.selectedColor) || 0;
    palette.innerHTML = '';
    for (let i = 0; i < 10; i++) {
        const div = document.createElement('div');
        div.className = 'color-option' + (i === selectedColor ? ' selected' : '');
        div.style.background = COLORS[i];
        div.dataset.colorIndex = i;
        div.addEventListener('click', function() {
            const idx = parseInt(this.dataset.colorIndex);
            palette.querySelectorAll('.color-option').forEach(opt => opt.classList.remove('selected'));
            this.classList.add('selected');
            document.getElementById('empAddBtn').dataset.selectedColor = idx;
            state.selectedColor = idx;
        });
        const check = document.createElement('span');
        check.className = 'check-mark';
        check.textContent = '✓';
        div.appendChild(check);
        palette.appendChild(div);
    }
}

// ===== CALENDAR =====
function renderCalendar() {
    const daysInMonth = getDaysInMonth(state.currentYear, state.currentMonth);
    const firstDay = getFirstDayOfMonth(state.currentYear, state.currentMonth);
    const today = new Date();
    const todayKey = formatDate(today.getFullYear(), today.getMonth(), today.getDate());

    document.getElementById('monthLabel').textContent = getMonthName(state.currentMonth) + ' ' + state.currentYear;
    document.getElementById('currentMonthLabel').textContent = getMonthName(state.currentMonth);

    const grid = document.getElementById('calendarGrid');
    grid.innerHTML = '';

    const dayNames = ['Dom','Seg','Ter','Qua','Qui','Sex','Sáb'];
    dayNames.forEach(name => {
        const div = document.createElement('div');
        div.className = 'day-name';
        div.textContent = name;
        grid.appendChild(div);
    });

    for (let i = 0; i < firstDay; i++) {
        const empty = document.createElement('div');
        empty.className = 'day-cell empty';
        grid.appendChild(empty);
    }

    const clientId = state.selectedClientId;

    for (let day = 1; day <= daysInMonth; day++) {
        const dateKey = formatDate(state.currentYear, state.currentMonth, day);
        const dayData = state.scaleData[dateKey];
        const isForClient = dayData && clientId && dayData.clienteId === clientId;
        const nome = (isForClient && dayData) ? dayData.nome : '';
        const isToday = dateKey === todayKey;

        const cell = document.createElement('div');
        cell.className = 'day-cell';
        cell.dataset.date = dateKey;

        let corIndex = -1;
        if (nome) {
            const emp = state.employees.find(e => e.nome === nome);
            corIndex = emp ? emp.cor : -1;
        }
        if (corIndex >= 0 && corIndex < 10) cell.classList.add('color-' + corIndex);

        if (isToday) {
            cell.classList.add('today');
            const badge = document.createElement('span');
            badge.className = 'today-badge';
            badge.textContent = 'HOJE';
            cell.appendChild(badge);
        }

        const num = document.createElement('div');
        num.className = 'day-number';
        num.textContent = day;
        cell.appendChild(num);

        const nameDiv = document.createElement('div');
        nameDiv.className = 'employee-name';
        const span = document.createElement('span');
        span.textContent = nome || '—';
        nameDiv.appendChild(span);
        cell.appendChild(nameDiv);

        if (!clientId) cell.style.opacity = '0.3';

        cell.addEventListener('click', function() {
            const dateKey2 = this.dataset.date;
            const data = state.scaleData[dateKey2];
            const clientId2 = state.selectedClientId;
            if (!clientId2) { showToast('Selecione um cliente primeiro.', 'info'); return; }
            if (data && data.clienteId === clientId2) {
                openViewModal(dateKey2);
            } else if (data && data.clienteId !== clientId2) {
                showToast('Este dia já tem plantão para outro cliente.', 'error');
            } else {
                openEditModal(dateKey2);
            }
        });

        grid.appendChild(cell);
    }

    applySearch();
    updateStats();
}

function updateStats() {
    const days = getDaysInMonth(state.currentYear, state.currentMonth);
    let count = 0;
    const clientId = state.selectedClientId;
    for (let d = 1; d <= days; d++) {
        const key = formatDate(state.currentYear, state.currentMonth, d);
        const data = state.scaleData[key];
        if (data && data.clienteId === clientId) count++;
    }
    document.getElementById('shiftCount').textContent = count;
    document.getElementById('employeeCount').textContent = state.employees.length;
}

function applySearch() {
    const term = state.searchTerm.toLowerCase().trim();
    const cells = document.querySelectorAll('.day-cell:not(.empty)');
    cells.forEach(cell => {
        const nameEl = cell.querySelector('.employee-name span');
        const name = nameEl ? nameEl.textContent || '' : '';
        const matches = name.toLowerCase().indexOf(term) !== -1;
        cell.style.display = matches ? '' : 'none';
    });
}

// ===== SHIFTS =====
function openEditModal(dateKey) {
    state.editingDate = dateKey;
    const parts = dateKey.split('-');
    const year = parseInt(parts[0]), month = parseInt(parts[1]) - 1, day = parseInt(parts[2]);
    
    document.getElementById('editModalTitle').textContent = 'Novo Plantão';
    document.getElementById('modalDate').textContent = 'Dia ' + day + ' de ' + getMonthName(month) + ' ' + year;

    const dayData = state.scaleData[dateKey] || {};
    const currentName = dayData.nome || '';
    
    const clientSelect = document.getElementById('modalClientSelect');
    if (clientSelect) {
        let clientHTML = '<option value="">Selecione o cliente *</option>';
        const active = state.clients.filter(c => c.status === 'ativo');
        active.forEach(c => {
            const selected = (c.id === state.selectedClientId || c.id === dayData.clienteId) ? 'selected' : '';
            clientHTML += `<option value="${c.id}" ${selected}>${c.nome}</option>`;
        });
        clientSelect.innerHTML = clientHTML;
    }
    
    const select = document.getElementById('modalEmployeeSelect');
    if (select) {
        select.innerHTML = '<option value="">Selecione o colaborador</option>';
        state.employees.forEach(emp => {
            const opt = document.createElement('option');
            opt.value = emp.nome;
            opt.textContent = emp.nome;
            if (emp.nome === currentName) opt.selected = true;
            select.appendChild(opt);
        });
    }
    
    document.getElementById('modalNameInput').value = currentName;
    openModal('editModal');
}

function saveEditModal() {
    const dateKey = state.editingDate;
    if (!dateKey) return;

    const clienteId = document.getElementById('modalClientSelect') ? document.getElementById('modalClientSelect').value : state.selectedClientId;
    const selected = document.getElementById('modalEmployeeSelect') ? document.getElementById('modalEmployeeSelect').value : '';
    const typed = document.getElementById('modalNameInput') ? document.getElementById('modalNameInput').value.trim() : '';

    if (!clienteId) { showToast('Selecione um cliente.', 'error'); return; }
    const client = state.clients.find(c => c.id === clienteId);
    if (!client) { showToast('Cliente inválido.', 'error'); return; }

    const nome = selected || typed;

    if (nome === '') {
        delete state.scaleData[dateKey];
    } else {
        let cor = -1;
        let emp = state.employees.find(e => e.nome === nome);
        if (emp) {
            cor = emp.cor;
        } else {
            const usedColors = state.employees.map(e => e.cor);
            for (let i = 0; i < 10; i++) {
                if (!usedColors.includes(i)) { cor = i; break; }
            }
            if (cor === -1) cor = 0;
            state.employees.push({ id: generateId(), nome, cor });
            renderEmployeeList();
        }
        state.scaleData[dateKey] = { nome, cor, clienteId, clienteNome: client.nome };
        state.history.unshift({ action: 'Salvou plantão do dia ' + dateKey + ' - ' + client.nome + ' / ' + nome, data: new Date().toLocaleString() });
    }

    closeModal('editModal');
    renderCalendar();
    renderHistory();
    showToast('Plantão salvo!', 'success');
}

function openViewModal(dateKey) {
    state.viewingDate = dateKey;
    const data = state.scaleData[dateKey];
    if (!data) { openEditModal(dateKey); return; }

    const parts = dateKey.split('-');
    const year = parseInt(parts[0]), month = parseInt(parts[1]) - 1, day = parseInt(parts[2]);
    const dateStr = day + ' de ' + getMonthName(month) + ' ' + year;

    const client = state.clients.find(c => c.id === data.clienteId);
    document.getElementById('viewClient').textContent = client ? client.nome : data.clienteNome || '-';
    document.getElementById('viewProfessional').textContent = data.nome || '-';
    document.getElementById('viewDate').textContent = dateStr;
    openModal('viewModal');
}

function deleteShift() {
    if (!confirm('Tem certeza que deseja excluir este plantão?')) return;
    const dateKey = state.viewingDate;
    if (!dateKey) return;
    delete state.scaleData[dateKey];
    state.history.unshift({ action: 'Excluiu plantão do dia ' + dateKey, data: new Date().toLocaleString() });
    closeModal('viewModal');
    renderCalendar();
    renderHistory();
    showToast('Plantão excluído!', 'success');
}

function editFromView() {
    closeModal('viewModal');
    if (state.viewingDate) openEditModal(state.viewingDate);
}

// ===== HISTORY =====
function renderHistory() {
    const list = document.getElementById('historyList');
    if (!list) return;
    if (state.history.length === 0) {
        list.innerHTML = '<p style="text-align:center;color:var(--text-tertiary);padding:20px;">Nenhuma alteração</p>';
        return;
    }
    let html = '';
    state.history.forEach(item => {
        html += `<div class="history-item"><span class="history-action">${item.action}</span><span class="history-date">${item.data}</span></div>`;
    });
    list.innerHTML = html;
}

// ===== REPORTS =====
function renderReports() {
    const monthSelect = document.getElementById('reportMonth');
    const yearSelect = document.getElementById('reportYear');
    if (monthSelect) {
        let html = '';
        MONTHS.forEach((m, i) => html += `<option value="${i}" ${i === state.currentMonth ? 'selected' : ''}>${m}</option>`);
        monthSelect.innerHTML = html;
    }
    if (yearSelect) {
        let html = '';
        [2024,2025,2026,2027,2028].forEach(y => html += `<option value="${y}" ${y === state.currentYear ? 'selected' : ''}>${y}</option>`);
        yearSelect.innerHTML = html;
    }
    generateReports();
}

function generateReports() {
    const month = parseInt(document.getElementById('reportMonth').value);
    const year = parseInt(document.getElementById('reportYear').value);
    const days = getDaysInMonth(year, month);
    const stats = {};
    let totalShifts = 0, totalHours = 0;
    for (let d = 1; d <= days; d++) {
        const key = formatDate(year, month, d);
        const data = state.scaleData[key];
        if (data && data.nome && data.nome.trim() !== '') {
            if (!stats[data.nome]) stats[data.nome] = { shifts: 0, hours: 0 };
            stats[data.nome].shifts++;
            totalShifts++;
            stats[data.nome].hours += 12;
            totalHours += 12;
        }
    }
    document.getElementById('statTotal').textContent = totalShifts;
    document.getElementById('statEmployees').textContent = Object.keys(stats).length;
    document.getElementById('statHours').textContent = totalHours + 'h';
    const names = Object.keys(stats);
    let maxShifts = 1;
    names.forEach(n => { if (stats[n].shifts > maxShifts) maxShifts = stats[n].shifts; });
    const chart = document.getElementById('reportChart');
    let chartHTML = '';
    names.forEach(name => {
        const pct = (stats[name].shifts / maxShifts) * 100;
        const label = name.length > 10 ? name.substring(0,10) + '…' : name;
        chartHTML += `<div class="chart-bar-wrapper"><div class="chart-bar" style="height:${Math.max(pct,4)}%;min-height:12px;"></div><div class="chart-bar-label" title="${name}">${label}</div><div class="chart-bar-value">${stats[name].shifts}</div></div>`;
    });
    chart.innerHTML = chartHTML || '<p style="text-align:center;color:var(--text-tertiary);padding:20px;">Sem dados para este mês</p>';
}

function exportCSV() {
    const month = parseInt(document.getElementById('reportMonth').value);
    const year = parseInt(document.getElementById('reportYear').value);
    const days = getDaysInMonth(year, month);
    const stats = {};
    for (let d = 1; d <= days; d++) {
        const key = formatDate(year, month, d);
        const data = state.scaleData[key];
        if (data && data.nome) {
            if (!stats[data.nome]) stats[data.nome] = { shifts: 0, hours: 0 };
            stats[data.nome].shifts++;
            stats[data.nome].hours += 12;
        }
    }
    let csv = 'Colaborador,Plantões,Horas\n';
    for (const name in stats) csv += name + ',' + stats[name].shifts + ',' + stats[name].hours + 'h\n';
    const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
    const link = document.createElement('a');
    link.href = URL.createObjectURL(blob);
    link.download = 'relatorio_' + year + '_' + (month + 1) + '.csv';
    link.click();
    showToast('CSV exportado!', 'success');
}

// ===== EXPORT =====
async function captureCalendar() {
    showLoading(true);
    try {
        const container = document.createElement('div');
        container.style.cssText = 'position:fixed;top:-9999px;left:-9999px;width:800px;background:#ffffff;padding:20px;border-radius:8px;z-index:9998;';

        const grid = document.getElementById('calendarGrid');
        const clone = grid.cloneNode(true);

        let totalShifts = 0;
        const clientId = state.selectedClientId;
        const days = getDaysInMonth(state.currentYear, state.currentMonth);
        for (let d = 1; d <= days; d++) {
            const key = formatDate(state.currentYear, state.currentMonth, d);
            const data = state.scaleData[key];
            if (data && data.clienteId === clientId) totalShifts++;
        }

        const client = state.clients.find(c => c.id === clientId);
        const clientName = client ? client.nome : 'Cliente não selecionado';
        const clientAddress = client ? client.endereco || '' : '';
        const clientObs = client ? client.observacao || '' : '';

        const headerHTML = `<div style="text-align:center;margin-bottom:16px;">
            <h1 style="font-size:22px;font-weight:700;color:#1A1A2E;letter-spacing:2px;margin:0;font-family:'Uber Move','Inter',sans-serif;">${state.companyName || 'Minha Empresa'}</h1>
            <p style="font-size:11px;color:#6B7280;margin:2px 0 0;font-weight:300;letter-spacing:2px;font-family:'Uber Move','Inter',sans-serif;">${state.companySubtitle || 'Sistema de Gestão de Escalas'}</p>
            <p style="font-size:9px;color:#9CA3AF;margin:4px 0 0;font-weight:300;letter-spacing:1px;font-family:'Uber Move','Inter',sans-serif;">Tecnologia <span style="color:#1E3A8A;font-weight:600;">PLANTONIX</span></p>
            <hr style="border:none;border-top:2px solid #E5E7EB;margin:12px 0;">
            <p style="font-size:14px;font-weight:600;color:#1F2937;margin:4px 0 0;font-family:'Uber Move','Inter',sans-serif;">Cliente: ${clientName}</p>
            ${clientAddress ? `<p style="font-size:12px;color:#6B7280;margin:2px 0 0;font-style:italic;font-family:'Uber Move','Inter',sans-serif;">${clientAddress}</p>` : ''}
            ${clientObs ? `<p style="font-size:12px;color:#6B7280;margin:2px 0 0;font-style:italic;font-family:'Uber Move','Inter',sans-serif;">${clientObs}</p>` : ''}
            <p style="font-size:14px;font-weight:600;color:#1F2937;margin:8px 0 0;font-family:'Uber Move','Inter',sans-serif;">${getMonthName(state.currentMonth)} ${state.currentYear}</p>
            <p style="font-size:12px;color:#6B7280;margin:4px 0 0;font-family:'Uber Move','Inter',sans-serif;">Total de plantões: ${totalShifts}</p>
            <hr style="border:none;border-top:2px solid #E5E7EB;margin:12px 0;">
        </div>`;

        container.innerHTML = headerHTML;
        container.appendChild(clone);

        const footerHTML = `<div style="text-align:center;margin-top:16px;font-size:10px;color:#9CA3AF;font-weight:300;letter-spacing:1px;font-family:'Uber Move','Inter',sans-serif;">
            Tecnologia <span style="color:#1E3A8A;font-weight:600;">PLANTONIX</span> · ${new Date().toLocaleDateString()}
        </div>`;

        const footerDiv = document.createElement('div');
        footerDiv.innerHTML = footerHTML;
        container.appendChild(footerDiv);

        document.body.appendChild(container);
        await new Promise(r => setTimeout(r, 300));

        const canvas = await html2canvas(container, {
            scale: 2,
            useCORS: true,
            logging: false,
            backgroundColor: '#ffffff',
            width: 800,
            height: container.scrollHeight
        });

        document.body.removeChild(container);
        showLoading(false);
        return canvas;
    } catch (error) {
        console.error('Erro ao capturar:', error);
        showLoading(false);
        showToast('Erro ao gerar imagem.', 'error');
        return null;
    }
}

async function shareScaleWhatsApp() {
    try {
        const canvas = await captureCalendar();
        if (!canvas) return;
        const blob = await new Promise(r => canvas.toBlob(r, 'image/png'));

        if (navigator.share && navigator.canShare) {
            const file = new File([blob], 'escala-plantonix.png', { type: 'image/png' });
            const shareData = { title: 'Escala PLANTONIX', text: 'Escala de ' + getMonthName(state.currentMonth) + ' ' + state.currentYear, files: [file] };
            if (navigator.canShare(shareData)) {
                await navigator.share(shareData);
                showToast('Escala compartilhada!', 'success');
                return;
            }
        }

        const link = document.createElement('a');
        link.href = URL.createObjectURL(blob);
        link.download = 'escala-' + getMonthName(state.currentMonth) + '-' + state.currentYear + '.png';

        const client = state.clients.find(c => c.id === state.selectedClientId);
        const clientName = client ? client.nome : 'Cliente não selecionado';

        const message = encodeURIComponent(
            '*Escala ' + (state.companyName || 'Minha Empresa') + ' - ' + getMonthName(state.currentMonth) + ' ' + state.currentYear + '*\n' +
            'Cliente: ' + clientName + '\n\n' +
            '📎 Anexo: imagem da escala\n\n' +
            'Tecnologia PLANTONIX'
        );

        window.open('https://wa.me/?text=' + message, '_blank');
        setTimeout(() => { link.click(); showToast('Imagem baixada! Anexe no WhatsApp.', 'success'); }, 1000);
    } catch (error) {
        console.error('Erro ao compartilhar:', error);
        showToast('Erro ao compartilhar.', 'error');
    }
}

async function downloadPNG() {
    try {
        const canvas = await captureCalendar();
        if (!canvas) return;
        const link = document.createElement('a');
        link.download = 'escala-' + getMonthName(state.currentMonth) + '-' + state.currentYear + '.png';
        link.href = canvas.toDataURL('image/png');
        link.click();
        showToast('Imagem baixada!', 'success');
    } catch (error) {
        console.error('Erro ao baixar PNG:', error);
        showToast('Erro ao baixar imagem.', 'error');
    }
}

async function downloadPDF() {
    try {
        const canvas = await captureCalendar();
        if (!canvas) return;
        const pdf = new jspdf.jsPDF({ orientation: 'landscape', unit: 'px', format: [canvas.width, canvas.height], hotfixes: ['px_scaling'] });
        const imgData = canvas.toDataURL('image/png');
        pdf.addImage(imgData, 'PNG', 0, 0, canvas.width, canvas.height);
        pdf.save('escala-' + getMonthName(state.currentMonth) + '-' + state.currentYear + '.pdf');
        showToast('PDF baixado!', 'success');
    } catch (error) {
        console.error('Erro ao baixar PDF:', error);
        showToast('Erro ao baixar PDF.', 'error');
    }
}

// ===== NAVIGATION =====
function navigateTo(page) {
    closeMenu();
    document.querySelectorAll('.side-menu-item').forEach(item => {
        item.classList.remove('active');
        if (item.dataset.page === page) item.classList.add('active');
    });
    switch(page) {
        case 'home': scrollToTop(); break;
        case 'calendar': document.getElementById('monthLabel').scrollIntoView({ behavior: 'smooth' }); break;
        case 'clients': openClientsModal(); break;
        case 'employees': openEmployeesModal(); break;
        case 'export': showToast('Escolha uma opção de exportação acima', 'info'); break;
        case 'settings': 
            document.getElementById('settingsCompanyName').value = state.companyName;
            document.getElementById('settingsCompanySubtitle').value = state.companySubtitle || '';
            openModal('settingsModal');
            break;
        case 'about': openModal('aboutModal'); break;
        case 'logout': handleLogout(); break;
    }
}

// ===== RENDER APP =====
function renderApp() {
    renderClientSelector();
    renderClientInfo();
    renderCalendar();
    renderEmployeeList();
    renderClientList();
    updateStats();
    applyTheme();
    renderColorPalette();
    renderReports();
    renderHistory();
    document.getElementById('fab').className = 'fab visible';
}

// ===== BACKUP =====
function exportBackup() {
    const data = {
        version: '16.0',
        date: new Date().toISOString(),
        employees: state.employees,
        clients: state.clients,
        scaleData: state.scaleData,
        history: state.history,
        companyName: state.companyName,
        companySubtitle: state.companySubtitle
    };
    const blob = new Blob([JSON.stringify(data, null, 2)], { type: 'application/json' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = 'plantonix_backup_' + formatDate(new Date().getFullYear(), new Date().getMonth(), new Date().getDate()) + '.json';
    a.click();
    URL.revokeObjectURL(url);
    showToast('Backup exportado!', 'success');
}

function importBackup(event) {
    const file = event.target.files[0];
    if (!file) return;
    const reader = new FileReader();
    reader.onload = function(e) {
        try {
            const data = JSON.parse(e.target.result);
            if (!data.employees || !data.clients || !data.scaleData) throw new Error('Arquivo inválido');
            if (!confirm('Isso vai substituir todos os dados. Continuar?')) return;
            state.employees = data.employees || [];
            state.clients = data.clients || [];
            state.scaleData = data.scaleData || {};
            if (data.history) state.history = data.history;
            if (data.companyName) state.companyName = data.companyName;
            if (data.companySubtitle) state.companySubtitle = data.companySubtitle;
            saveCompanySettings(state.companyName, state.companySubtitle);
            state.history.unshift({ action: 'Backup restaurado', data: new Date().toLocaleString() });
            renderApp();
            renderEmployeeList();
            renderClientList();
            renderHistory();
            showToast('Backup restaurado!', 'success');
        } catch (err) {
            showToast('Erro ao restaurar backup.', 'error');
        }
    };
    reader.readAsText(file);
    event.target.value = '';
}

// ===== INIT =====
document.addEventListener('DOMContentLoaded', function() {
    // Splash
    setTimeout(() => {
        document.getElementById('splashScreen').classList.add('hide');
        setTimeout(() => { document.getElementById('splashScreen').style.display = 'none'; }, 700);
    }, 1800);

    // Theme
    applyTheme();

    // Events
    document.getElementById('menuIcon').addEventListener('click', toggleMenu);
    document.getElementById('sideMenuClose').addEventListener('click', closeMenu);
    document.getElementById('menuOverlay').addEventListener('click', closeMenu);
    
    document.querySelectorAll('.side-menu-item').forEach(item => {
        item.addEventListener('click', () => navigateTo(item.dataset.page));
    });

    document.getElementById('themeToggle').addEventListener('click', toggleTheme);
    document.getElementById('headerLogout').addEventListener('click', handleLogout);
    document.getElementById('loginBtn').addEventListener('click', handleLogin);
    document.getElementById('passwordToggle').addEventListener('click', function(e) {
        togglePassword('loginPassword', this);
    });

    document.getElementById('clientSelector').addEventListener('change', selectClient);

    document.getElementById('prevMonthBtn').addEventListener('click', function() {
        state.currentMonth--;
        if (state.currentMonth < 0) { state.currentMonth = 11; state.currentYear--; }
        renderCalendar();
    });

    document.getElementById('nextMonthBtn').addEventListener('click', function() {
        state.currentMonth++;
        if (state.currentMonth > 11) { state.currentMonth = 0; state.currentYear++; }
        renderCalendar();
    });

    document.getElementById('searchInput').addEventListener('input', function(e) {
        state.searchTerm = e.target.value;
        document.getElementById('searchClear').style.display = state.searchTerm ? 'block' : 'none';
        applySearch();
    });

    document.getElementById('searchClear').addEventListener('click', function() {
        state.searchTerm = '';
        document.getElementById('searchInput').value = '';
        this.style.display = 'none';
        document.querySelectorAll('.day-cell:not(.empty)').forEach(c => c.style.display = '');
    });

    document.getElementById('fabMain').addEventListener('click', function() {
        this.classList.toggle('open');
        document.getElementById('fabActions').classList.toggle('open');
    });

    document.getElementById('fabNewShift').addEventListener('click', function() {
        document.getElementById('fabMain').classList.remove('open');
        document.getElementById('fabActions').classList.remove('open');
        if (!state.selectedClientId) { showToast('Selecione um cliente primeiro.', 'info'); return; }
        const today = new Date();
        openEditModal(formatDate(today.getFullYear(), today.getMonth(), today.getDate()));
    });

    document.getElementById('fabShare').addEventListener('click', function() {
        document.getElementById('fabMain').classList.remove('open');
        document.getElementById('fabActions').classList.remove('open');
        shareScaleWhatsApp();
    });

    document.getElementById('downloadPDFBtn').addEventListener('click', downloadPDF);
    document.getElementById('downloadPNGBtn').addEventListener('click', downloadPNG);
    document.getElementById('shareWhatsAppBtn').addEventListener('click', shareScaleWhatsApp);

    document.getElementById('openClientsBtn').addEventListener('click', openClientsModal);
    document.getElementById('openEmployeesBtn').addEventListener('click', openEmployeesModal);

    document.getElementById('brandSubname').addEventListener('click', function() {
        document.getElementById('settingsCompanyName').value = state.companyName;
        document.getElementById('settingsCompanySubtitle').value = state.companySubtitle || '';
        openModal('settingsModal');
    });

    // Modals
    document.querySelectorAll('.modal').forEach(modal => {
        modal.addEventListener('click', function(e) { if (e.target === this) this.classList.remove('active'); });
    });

    document.getElementById('editModalCancel').addEventListener('click', () => closeModal('editModal'));
    document.getElementById('editModalSave').addEventListener('click', saveEditModal);

    document.getElementById('viewModalClose').addEventListener('click', () => closeModal('viewModal'));
    document.getElementById('viewModalEdit').addEventListener('click', editFromView);
    document.getElementById('viewModalDelete').addEventListener('click', deleteShift);

    document.getElementById('clientsModalClose').addEventListener('click', () => closeModal('clientsModal'));
    document.getElementById('clientAddBtn').addEventListener('click', function() {
        if (this.dataset.editId) updateClient(this.dataset.editId);
        else addClient();
    });

    document.getElementById('employeesModalClose').addEventListener('click', () => closeModal('employeesModal'));
    document.getElementById('empAddBtn').addEventListener('click', function() {
        if (this.dataset.editId) updateEmployee(this.dataset.editId);
        else addEmployee();
    });

    document.getElementById('settingsModalCancel').addEventListener('click', () => closeModal('settingsModal'));
    document.getElementById('settingsModalSave').addEventListener('click', function() {
        const name = document.getElementById('settingsCompanyName').value.trim();
        const subtitle = document.getElementById('settingsCompanySubtitle').value.trim();
        if (!name) { showToast('Digite o nome da empresa.', 'error'); return; }
        saveCompanySettings(name, subtitle);
        closeModal('settingsModal');
    });

    document.getElementById('aboutModalClose').addEventListener('click', () => closeModal('aboutModal'));

    document.getElementById('btnTop').addEventListener('click', scrollToTop);
    window.addEventListener('scroll', function() {
        const btn = document.getElementById('btnTop');
        if (window.scrollY > 300) btn.classList.add('visible');
        else btn.classList.remove('visible');
    });

    // Reports
    document.getElementById('reportMonth').addEventListener('change', generateReports);
    document.getElementById('reportYear').addEventListener('change', generateReports);

    // Init app
    renderApp();
    console.log('PLANTONIX v16.0 - Pronto para uso!');
    console.log('🎨 Paleta: Preto + Branco + Azul Galáxia');
    console.log('🔤 Fonte: Uber Move');
    console.log('📱 Botão WhatsApp padronizado');
    console.log('ℹ️ Tela "Sobre" com posicionamento SaaS');
});
</script>

</body>
</html>