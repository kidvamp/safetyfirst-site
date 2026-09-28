<!DOCTYPE html>
<html lang="mn">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<meta name="color-scheme" content="light only">
<title>SafetyF/rst — Гэр бүлд зориулсан интернетийн аюулгүй байдал</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@500;600;700;800;900&family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
<style>
  :root {
    color-scheme: light;
    --indigo: #4B47A6;
    --indigo-dark: #2D2A5C;
    --indigo-soft: #EEF2FB;
    --blue-light: #DCE8F8;
    --blue-mid: #B6CDE8;
    --cream: #FBF6EC;
    --ink: #2D3748;
    --sub: #4A5568;
    --yellow: #F6E05E;
    --yellow-line: #ECC94B;
    --coral: #E53E3E;
    --pink: #ED64A6;
    --lime: #84CC16;
    --green: #2F855A;
    --white: #FFFFFF;
    --line: rgba(45,55,72,0.12);
    --safe-t: env(safe-area-inset-top, 0px);
    --safe-b: env(safe-area-inset-bottom, 0px);
    --tv-width: 620px;
    --tv-height: 260px;
  }

  * { box-sizing: border-box; }
  html { scroll-padding-top: var(--safe-t); }
  html, body { height: 100%; }
  body {
    margin: 0;
    background:
      radial-gradient(circle at 1px 1px, rgba(75,71,166,0.14) 1.4px, transparent 0) 0 0/24px 24px,
      linear-gradient(160deg, var(--blue-light), var(--blue-mid));
    background-attachment: fixed;
    color: var(--ink);
    font-family: 'Inter', sans-serif;
    padding-top: var(--safe-t);
    padding-bottom: var(--safe-b);
    -webkit-font-smoothing: antialiased;
  }
  img, svg { max-width: 100%; }
  button { font-family: inherit; cursor: pointer; }
  a { color: inherit; }
  h1, h2, h3 { font-family: 'Poppins', sans-serif; color: #1A202C; }

  .view { display: none; min-height: calc(100vh - var(--safe-t) - var(--safe-b)); position: relative; overflow: hidden; }
  .view.active { display: block; }

  #transitionOverlay {
    position: fixed; inset: 0; z-index: 9999;
    background: var(--indigo);
    transform: translateX(-100%);
    display: flex; align-items: center; justify-content: center;
    pointer-events: none;
  }
  #transitionOverlay span {
    font-family: 'Poppins', sans-serif; font-weight: 800; font-size: 1.6rem; color: #fff; opacity: 0.9;
  }

  .blob { position: absolute; border-radius: 50%; filter: blur(46px); opacity: 0.3; z-index: 0; pointer-events: none; animation: floatBlob 11s ease-in-out infinite; }
  .blob-a { width: 260px; height: 260px; background: #93C5FD; top: -70px; left: -70px; }
  .blob-b { width: 300px; height: 300px; background: #BFDBFE; bottom: -100px; right: -80px; animation-duration: 14s; }
  .blob-c { width: 200px; height: 200px; background: #DDD6FE; top: 45%; right: 8%; animation-duration: 9s; animation-delay: 1s; }
  .blob-d { width: 220px; height: 220px; background: #C7D2FE; bottom: 10%; left: 6%; animation-duration: 12s; animation-delay: 0.5s; }

  .sticker { position: absolute; z-index: 2; pointer-events: none; animation: bob 4s ease-in-out infinite; }
  .sticker.spin { animation: spinSlow 9s linear infinite; }
  @keyframes spinSlow { from { transform: rotate(0deg); } to { transform: rotate(360deg); } }

  @keyframes floatBlob { 0%,100% { transform: translate(0,0) rotate(0deg); } 50% { transform: translate(18px,-22px) rotate(10deg); } }
  @keyframes bob { 0%,100% { transform: translateY(0) rotate(var(--rot,0deg)); } 50% { transform: translateY(-10px) rotate(var(--rot,0deg)); } }
  @keyframes popIn { from { opacity: 0; transform: translateY(26px) scale(0.94); } to { opacity: 1; transform: translateY(0) scale(1); } }
  @keyframes popInRight { from { opacity: 0; transform: translateX(46px); } to { opacity: 1; transform: translateX(0); } }
  @keyframes popInLeft { from { opacity: 0; transform: translateX(-46px); } to { opacity: 1; transform: translateX(0); } }
  @keyframes confettiFall { to { transform: translateY(110vh) rotate(540deg); opacity: 0.85; } }

  .stagger-in > * { opacity: 0; animation: popIn 0.6s cubic-bezier(.22,1,.36,1) forwards; }
  .stagger-in > *:nth-child(1) { animation-delay: .04s; }
  .stagger-in > *:nth-child(2) { animation-delay: .12s; }
  .stagger-in > *:nth-child(3) { animation-delay: .20s; }
  .stagger-in > *:nth-child(4) { animation-delay: .28s; }
  .stagger-in > *:nth-child(5) { animation-delay: .36s; }
  .stagger-in > *:nth-child(6) { animation-delay: .44s; }
  .stagger-in > *:nth-child(7) { animation-delay: .52s; }
  .stagger-in > *:nth-child(8) { animation-delay: .60s; }

  .confetti-piece { position: fixed; top: -24px; width: 9px; height: 15px; z-index: 9998; pointer-events: none; animation: confettiFall linear forwards; }

  .pop-card { border: 2.5px solid var(--ink); box-shadow: 6px 6px 0 rgba(45,55,72,0.15); border-radius: 18px; background: rgba(255,255,255,0.92); backdrop-filter: blur(8px); }
  .btn-pop { border: 2.5px solid var(--ink); box-shadow: 3px 3px 0 var(--ink); transition: transform .15s ease, box-shadow .15s ease; }
  .btn-pop:hover { transform: translate(-2px,-2px); box-shadow: 5px 5px 0 var(--ink); }
  .btn-pop:active { transform: translate(2px,2px); box-shadow: 1px 1px 0 var(--ink); }

  .marker-yellow { background: linear-gradient(#FEFCBF, #FEFCBF); background-repeat: no-repeat; background-size: 100% 42%; background-position: 0 88%; padding: 0 .12em; }
  .marker-coral { background: linear-gradient(#FED7D7, #FED7D7); background-repeat: no-repeat; background-size: 100% 34%; background-position: 0 90%; padding: 0 .12em; }

  #view-landing.active { display: block; }
  .landing-hero-wrap { position: relative; min-height: calc(100vh - var(--safe-t) - var(--safe-b)); display: flex; align-items: center; justify-content: center; padding: 1.5rem 1.25rem; }
  .landing-extra { position: relative; z-index: 1; padding: 3.5rem 1.5rem 4.5rem; background: linear-gradient(180deg, rgba(255,255,255,0.35), rgba(255,255,255,0.05)); }
  .extra-inner { max-width: 1020px; margin: 0 auto; text-align: center; }
  .extra-title { font-size: clamp(1.6rem, 4vw, 2.2rem); margin: 0 0 0.6rem; color: #1A202C; }
  .extra-sub { margin: 0 0 2rem; color: var(--sub); font-size: 1.02rem; }
  .feature-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(220px, 1fr)); gap: 1.3rem; text-align: left; }
  .feature-card { padding: 1.5rem 1.4rem; background: rgba(255,255,255,0.94); transition: transform .18s ease, box-shadow .18s ease; }
  .feature-card:hover { transform: translate(-3px,-3px); box-shadow: 9px 9px 0 var(--indigo); }
  .feature-emoji { font-size: 2rem; margin-bottom: 0.6rem; }
  .feature-card h3 { margin: 0 0 0.4rem; font-size: 1.08rem; color: #1A202C; }
  .feature-card p { margin: 0; font-size: 0.93rem; color: var(--sub); line-height: 1.55; }
  .extra-footnote { margin-top: 2.4rem; font-size: 0.85rem; color: var(--sub); }
  .scroll-hint { position: absolute; bottom: 18px; left: 50%; transform: translateX(-50%); display: flex; flex-direction: column; align-items: center; gap: 4px; color: var(--indigo-dark); font-size: 0.78rem; font-weight: 700; opacity: 0.75; animation: bob 2s ease-in-out infinite; }
  .landing-card {
    position: relative; z-index: 1;
    width: 100%;
    max-width: 1240px;
    display: flex;
    background: rgba(255,255,255,0.92);
    backdrop-filter: blur(8px);
    border-radius: 30px;
    overflow: hidden;
  }
  @media (max-width: 780px) { .landing-card { flex-direction: column; } }

  .landing-left {
    flex: 1;
    background: linear-gradient(160deg, rgba(224,242,254,0.7), rgba(186,230,253,0.7));
    padding: 3.2rem 3rem;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    min-height: 620px;
  }

  .brand-mark { display: inline-flex; align-items: center; }
  .brand-text { font-weight: 900; font-size: clamp(2.6rem, 6vw, 3.6rem); color: #0d0d0d; letter-spacing: -0.03em; }
  .slash-wrap { position: relative; display: inline-block; color: var(--indigo); }
  .brand-dot { position: absolute; top: -0.5em; left: 50%; transform: translateX(-50%); width: 12px; height: 12px; border-radius: 50%; background: var(--indigo); }
  .brand-tag { margin: 0.7rem 0 0; color: var(--sub); font-size: 1.15rem; max-width: 32ch; }
  .hero-art { flex: 1; display: flex; align-items: center; justify-content: center; padding: 1.5rem 0; position: relative; }
  .hero-art svg { width: 100%; max-width: 340px; height: auto; }
  .social-icons { display: flex; gap: 1rem; }
  .social-icons a { display: inline-flex; padding: 0.55rem; border-radius: 12px; border: 2.5px solid var(--ink); background: #fff; box-shadow: 3px 3px 0 var(--ink); transition: transform .15s ease; }
  .social-icons a:hover { transform: translate(-2px,-2px); }
  .social-icons svg { width: 24px; height: 24px; }

  .landing-right {
    flex: 1.15;
    padding: 3.4rem 3.2rem;
    display: flex;
    flex-direction: column;
    justify-content: center;
  }
  .mission-label { margin: 0 0 0.9rem; font-size: 1.4rem; font-weight: 800; letter-spacing: 0.02em; color: var(--indigo); }
  .mission-text { margin: 0; color: var(--sub); line-height: 1.7; font-size: 1.15rem; }
  .mission-more { margin: 0.6rem 0 0; color: var(--sub); line-height: 1.7; font-size: 1.15rem; max-height: 0; overflow: hidden; transition: max-height 0.3s ease; }
  .mission-more.open { max-height: 200px; }
  .more-info-btn { align-self: flex-start; margin-top: 1.1rem; border: none; background: transparent; color: var(--indigo); font-weight: 700; font-size: 1.02rem; padding: 0; border-bottom: 2px solid var(--indigo); padding-bottom: 2px; }

  .entry-row { display: flex; gap: 1.3rem; margin-top: 2.4rem; }
  @media (max-width: 480px) { .entry-row { flex-direction: column; } }
  .entry-btn {
    flex: 1;
    border-radius: 20px;
    padding: 1.7rem 1.2rem 1.4rem;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 0.8rem;
    text-align: center;
    transition: transform 0.2s cubic-bezier(.34,1.56,.64,1), box-shadow 0.2s ease;
  }
  .entry-btn:hover, .entry-btn:focus-visible { transform: translateY(-6px) scale(1.02); }
  .entry-btn:focus-visible { outline: 2px solid #fff; outline-offset: 3px; }
  .entry-child { background: linear-gradient(160deg, #EEECFC, #D6D1F5); box-shadow: 4px 4px 0 var(--indigo); }
  .entry-adult { background: linear-gradient(160deg, #FFF0EA, #FFD9CC); box-shadow: 4px 4px 0 var(--coral); }
  .entry-btn svg { width: 104px; height: 104px; }
  .entry-icon-bg { display: none; }
  .entry-btn span { font-weight: 800; font-size: 1.15rem; color: var(--ink); }

  .view-content { position: relative; z-index: 1; }
  .back-btn {
    position: sticky; top: var(--safe-t); z-index: 4;
    display: inline-flex; align-items: center; gap: 0.4rem;
    margin: 1rem; padding: 0.55rem 1.1rem; border-radius: 999px;
    border: 2.5px solid var(--ink); font-weight: 700; font-size: 0.92rem;
    background: var(--white); color: var(--indigo-dark);
    box-shadow: 3px 3px 0 var(--ink);
  }

  #view-kids { background: linear-gradient(180deg, #E9F3FF, #D3E4FB); color: var(--ink); }
  .kids-hero { text-align: center; padding: 0.5rem 1.5rem 0; position: relative; }
  .kids-hero h1 { font-size: clamp(1.8rem, 5vw, 2.5rem); margin: 0 0 0.3rem; color: var(--indigo-dark); }
  .kids-hero p { max-width: 46ch; margin: 0 auto; font-size: 1.02rem; color: var(--sub); }

  /* Resize the TV by changing --tv-width and --tv-height in :root above — as much as you want. */
  .story { max-width: var(--tv-width); margin: 1.5rem auto 0; padding: 0 1.25rem 2rem; }

  .tv-frame {
    display: flex;
    background: var(--indigo);
    border-radius: 26px;
    padding: 14px;
    transform: rotate(-0.6deg);
  }
  .tv-screen { flex: 1; background: var(--white); border-radius: 14px; padding: 1.1rem; display: flex; flex-direction: column; overflow: hidden; }
  .tv-controls { width: 46px; margin-left: 12px; display: flex; flex-direction: column; align-items: center; justify-content: space-between; padding: 4px 0; }
  .tv-dial { width: 26px; height: 26px; border-radius: 50%; background: var(--indigo-dark); border: 3px solid rgba(255,255,255,0.35); }
  .tv-speaker { display: grid; grid-template-columns: repeat(2, 1fr); gap: 4px; }
  .tv-speaker span { width: 8px; height: 8px; border-radius: 50%; background: rgba(255,255,255,0.35); }

  .stage-inner img { display: block; width: 100%; height: var(--tv-height); object-fit: cover; border-radius: 10px; }
  .stage-placeholder {
    width: 100%; height: var(--tv-height); border-radius: 10px;
    border: 3px dashed var(--indigo); background: var(--indigo-soft);
    display: flex; align-items: center; justify-content: center;
    text-align: center; padding: 1rem; color: var(--indigo-dark); font-weight: 700; font-size: 0.95rem;
  }
  .stage-caption { margin-top: 0.9rem; font-size: 1.1rem; font-weight: 700; text-align: center; color: var(--indigo-dark); }
  .stage-lesson { margin-top: 0.35rem; text-align: center; font-size: 0.96rem; color: var(--sub); line-height: 1.5; }

  .story-controls { display: flex; align-items: center; justify-content: space-between; margin-top: 1.1rem; gap: 0.75rem; }
  .story-nav-btn { background: var(--indigo); color: #fff; font-weight: 700; padding: 0.65rem 1.2rem; border-radius: 999px; font-size: 0.95rem; }
  .story-nav-btn:disabled { opacity: 0.35; }
  .story-nav-btn.next { background: var(--coral); }
  .dots { display: flex; gap: 0.4rem; }
  .dot { width: 8px; height: 8px; border-radius: 50%; background: var(--blue-mid); transition: transform .2s ease, background .2s ease; }
  .dot.on { background: var(--pink); transform: scale(1.3); }

  .kids-quiz { max-width: 560px; margin: 0.5rem auto 3rem; padding: 0 1.25rem; }
  .quiz-card { background: var(--white); border-radius: 18px; overflow: hidden; margin-top: 1rem; }
  .quiz-topbar { background: linear-gradient(90deg, var(--indigo), var(--pink)); height: 9px; }
  .quiz-body { padding: 1.3rem 1.4rem; }
  .quiz-body h3 { margin: 0 0 0.9rem; font-size: 1.15rem; color: var(--indigo-dark); }
  .quiz-options { display: grid; gap: 0.55rem; }
  .quiz-opt { border: 2px solid var(--line); background: transparent; color: var(--ink); padding: 0.7rem 0.9rem; border-radius: 10px; text-align: left; font-size: 0.98rem; font-weight: 500; display: flex; align-items: center; gap: 0.6rem; transition: transform .12s ease; }
  .quiz-opt::before { content: ""; width: 16px; height: 16px; border-radius: 50%; border: 2px solid var(--indigo); flex-shrink: 0; }
  .quiz-opt:hover { border-color: var(--indigo); transform: translateX(3px); }
  .quiz-opt.correct { background: #E6FFFA; border-color: var(--green); }
  .quiz-opt.correct::before { background: var(--green); border-color: var(--green); }
  .quiz-opt.wrong { background: #FFF5F5; border-color: var(--coral); }
  .quiz-opt.wrong::before { background: var(--coral); border-color: var(--coral); }
  .quiz-feedback { margin-top: 0.8rem; font-weight: 700; min-height: 1.4em; font-size: 0.94rem; }
  .quiz-result { text-align: center; padding: 0.5rem 0.5rem 1rem; }
  .quiz-result .badge { font-size: 3.2rem; display: inline-block; animation: bob 1.6s ease-in-out infinite; }
  .quiz-result h3 { font-size: 1.4rem; margin: 0.3rem 0; color: var(--indigo-dark); }
  .kid-feedback-form { display: grid; gap: 0.8rem; margin-top: 0.8rem; }
  .kid-feedback-form label { font-weight: 700; font-size: 0.92rem; display: block; margin-bottom: 0.35rem; }
  .kid-feedback-form textarea { width: 100%; border-radius: 10px; border: 2px solid var(--line); padding: 0.6rem 0.8rem; font-family: inherit; font-size: 0.95rem; }
  .emoji-row { display: flex; gap: 0.6rem; }
  .emoji-choice { border: 2px solid var(--line); background: #fff; border-radius: 10px; padding: 0.5rem 0.9rem; font-size: 1.3rem; transition: transform .15s ease; }
  .emoji-choice:hover { transform: scale(1.15); }
  .emoji-choice.picked { border-color: var(--coral); background: #FFF5F5; }
  .submit-btn { background: var(--indigo); color: #fff; border-radius: 999px; padding: 0.7rem 1.3rem; font-weight: 700; font-size: 0.95rem; justify-self: start; }
  .thanks-note { font-weight: 700; color: var(--green); margin-top: 0.6rem; }
  .device-note { font-size: 0.82rem; color: var(--sub); margin-top: 0.5rem; }

  #view-adults { background: var(--cream); color: var(--ink); }
  .adults-hero { max-width: 760px; margin: 0 auto; padding: 0.5rem 1.5rem 0.5rem; position: relative; }
  .adults-hero h1 { font-size: clamp(1.9rem, 4.5vw, 2.6rem); line-height: 1.18; margin: 0 0 0.6rem; font-weight: 800; }
  .adults-hero p { max-width: 62ch; font-size: 1.05rem; color: var(--sub); line-height: 1.6; margin: 0; }

  .callout {
    position: relative; z-index: 1;
    max-width: 760px; margin: 1.8rem 1.5rem 0;
    padding: 1.3rem 1.5rem 1.5rem;
    background: #FEFCBF; border: 2.5px solid var(--ink);
    border-radius: 12px; box-shadow: 6px 6px 0 rgba(45,55,72,0.15);
    transform: rotate(-0.7deg);
  }
  @media (min-width: 830px) { .callout { margin-left: auto; margin-right: auto; } }
  .callout h3 { margin: 0 0 0.7rem; font-size: 1.1rem; }
  .callout ul { margin: 0; padding-left: 1.2rem; }
  .callout li { margin-bottom: 0.5rem; line-height: 1.5; font-size: 0.96rem; }
  .callout li strong { font-weight: 800; }

  .section-label { max-width: 900px; margin: 2.6rem auto 0.9rem; padding: 0 1.5rem; font-size: 1.35rem; font-weight: 800; }
  .resource-grid { max-width: 900px; margin: 0 auto; padding: 0 1.5rem; display: grid; grid-template-columns: repeat(auto-fit, minmax(240px, 1fr)); gap: 1.1rem; }
  .resource-card { background: rgba(255,255,255,0.92); backdrop-filter: blur(8px); border-radius: 14px; padding: 1.1rem 1.2rem; transition: transform .18s ease, box-shadow .18s ease; }
  .resource-card:hover { transform: translate(-3px,-3px); box-shadow: 8px 8px 0 var(--indigo); }
  .resource-card h3 { margin: 0 0 0.4rem; font-size: 1.02rem; font-weight: 800; }
  .resource-card p { margin: 0 0 0.7rem; font-size: 0.93rem; color: var(--sub); line-height: 1.55; }
  .resource-card a { font-size: 0.9rem; font-weight: 700; color: var(--indigo); text-decoration: none; border-bottom: 2px solid var(--indigo); }

  .further-links { max-width: 900px; margin: 1.8rem auto 0; padding: 1.1rem 1.5rem; border-top: 2.5px dashed var(--ink); border-bottom: 2.5px dashed var(--ink); }
  .further-links p.lead { margin: 0 0 0.6rem; font-size: 0.9rem; color: var(--sub); font-weight: 600; }
  .link-row { display: flex; flex-wrap: wrap; gap: 0.5rem 1.2rem; }
  .link-row a { font-size: 0.9rem; font-weight: 700; color: var(--indigo); text-decoration: none; }
  .link-row a:hover { text-decoration: underline; }

  .survey { max-width: 600px; margin: 2.4rem auto 3.5rem; padding: 0 1.5rem; }
  .survey-card { background: rgba(255,255,255,0.92); backdrop-filter: blur(8px); border-radius: 16px; overflow: hidden; }
  .survey-topbar { height: 11px; background: linear-gradient(90deg, var(--indigo), var(--coral)); }
  .survey-body { padding: 1.5rem 1.6rem; }
  .survey-body h3 { font-weight: 800; margin: 0 0 0.3rem; font-size: 1.2rem; }
  .survey-body > p { margin: 0 0 1.1rem; color: var(--sub); font-size: 0.93rem; }
  .field { margin-bottom: 1.1rem; padding-bottom: 1rem; border-bottom: 1.5px dashed var(--line); }
  .field label { display: block; font-weight: 700; font-size: 0.95rem; margin-bottom: 0.5rem; }
  .field .hint { font-weight: 400; color: var(--sub); font-size: 0.83rem; }
  .radio-group { display: grid; gap: 0.5rem; }
  .radio-group label { font-weight: 400; display: flex; align-items: center; gap: 0.55rem; font-size: 0.95rem; }
  select, textarea, input[type=text] { width: 100%; border-radius: 8px; border: 2px solid var(--line); padding: 0.55rem 0.6rem; font-family: inherit; font-size: 0.95rem; background: #fff; }
  select:focus, textarea:focus, input:focus { outline: none; border-color: var(--indigo); }
  .a-submit { background: var(--indigo); color: #fff; border-radius: 8px; padding: 0.7rem 1.4rem; font-weight: 700; font-size: 0.95rem; }
  .a-export { background: #fff; color: var(--ink); border-radius: 8px; padding: 0.7rem 1.1rem; font-weight: 700; font-size: 0.9rem; margin-left: 0.6rem; }
  .response-count { font-size: 0.83rem; color: var(--sub); margin-top: 0.8rem; padding: 0 1.6rem 1.3rem; }
  .study-note { max-width: 600px; margin: 0 auto 3rem; padding: 0 1.5rem; font-size: 0.83rem; color: var(--sub); line-height: 1.6; }

  @media (prefers-reduced-motion: reduce) {
    * { transition: none !important; animation: none !important; }
    .stagger-in > * { opacity: 1 !important; }
    .blob { display: none; }
  }
</style>
</head>
<body>

<div id="transitionOverlay" aria-hidden="true"><span>SafetyF/rst</span></div>

<div id="app">

  <section id="view-landing" class="view active">
    <div class="landing-hero-wrap">
      <div class="blob blob-a"></div>
      <div class="blob blob-b"></div>
      <div class="blob blob-c"></div>
      <svg class="sticker" style="width:36px;height:36px;top:8%;right:12%;--rot:-10deg;" viewBox="0 0 24 24"><path d="M12 0 L14.5 9 L24 12 L14.5 15 L12 24 L9.5 15 L0 12 L9.5 9 Z" fill="var(--pink)"/></svg>
      <svg class="sticker" style="width:24px;height:24px;bottom:10%;left:9%;--rot:12deg;animation-delay:.5s;" viewBox="0 0 24 24"><circle cx="12" cy="12" r="11" fill="var(--lime)"/></svg>
      <svg class="sticker spin" style="width:28px;height:28px;top:14%;left:6%;" viewBox="0 0 24 24"><path d="M12 0 L14.5 9 L24 12 L14.5 15 L12 24 L9.5 15 L0 12 L9.5 9 Z" fill="var(--yellow)"/></svg>
      <div class="landing-card pop-card stagger-in">
        <div class="landing-left">
          <div>
            <div class="brand-mark">
              <span class="brand-text">SafetyF<span class="slash-wrap"><span class="brand-dot"></span>/</span>rst</span>
            </div>
            <p class="brand-tag">Хүүхдийнхээ аюулгүй байдлыг алхам алхмаар хангацгаая.</p>
          </div>
          <div class="hero-art">
            <svg viewBox="0 0 220 150" xmlns="http://www.w3.org/2000/svg" fill="none" stroke="var(--indigo-dark)" stroke-width="3" stroke-linecap="round" stroke-linejoin="round">
              <rect x="55" y="20" width="110" height="74" rx="6"/>
              <path d="M80 60 l14 14 20 -28" stroke="var(--green)"/>
              <path d="M30 110 h160 l-14 22 h-132 z"/>
              <path d="M95 110 h30"/>
            </svg>
          </div>
          <div class="social-icons">
            <a href="#" aria-label="Имэйл"><svg viewBox="0 0 24 24" fill="none" stroke="var(--indigo-dark)" stroke-width="2"><rect x="3" y="5" width="18" height="14" rx="2"/><path d="M3 7l9 6 9-6"/></svg></a>
          </div>
        </div>
        <div class="landing-right">
          <h2 class="mission-label">Бидний эрхэм зорилго</h2>
          <p class="mission-text">Бид хүүхдүүдэд <span class="marker-yellow">итгэлтэйгээр</span> интернетийг судлахад нь тусалж, эцэг эх, багш нарт тэднийг чиглүүлэх хэрэгслүүдийг өгдөг — үлгэр, товч хичээл, хоёр талд зориулсан эх сурвалжуудаар дамжуулан.</p>
          <p class="mission-more" id="missionMore">SafetyF/rst нь хүүхдийн цахим аюулгүй байдлын талаарх сургуулийн судалгааны төслийн хүрээнд бүтээгдсэн. Энд үлдээсэн таны хариулт бүр тухайн судалгаанд шууд ашиглагдана.</p>
          <button class="more-info-btn" id="moreInfoBtn">Дэлгэрэнгүй</button>

          <div class="entry-row">
            <button class="entry-btn entry-child btn-pop" data-target="kids" aria-label="Хүүхдийн хэсэгт орох">
              <svg viewBox="0 0 100 120" xmlns="http://www.w3.org/2000/svg">
                <circle class="entry-icon-bg" cx="50" cy="58" r="48"/>
                <circle cx="50" cy="30" r="20" fill="#F5B98A"/>
                <path d="M30 24 Q50 4 70 24 Q70 14 50 12 Q30 14 30 24 Z" fill="#2B2440"/>
                <rect x="26" y="52" width="48" height="50" rx="16" fill="var(--indigo)"/>
                <circle cx="42" cy="30" r="2.4" fill="#2B2440"/>
                <circle cx="58" cy="30" r="2.4" fill="#2B2440"/>
                <path d="M42 38 Q50 44 58 38" stroke="#2B2440" stroke-width="2.4" fill="none" stroke-linecap="round"/>
              </svg>
              <span>Би хүүхэд</span>
            </button>
            <button class="entry-btn entry-adult btn-pop" data-target="adults" aria-label="Эцэг эх, багш нарын хэсэгт орох">
              <svg viewBox="0 0 100 120" xmlns="http://www.w3.org/2000/svg">
                <circle class="entry-icon-bg" cx="50" cy="56" r="48"/>
                <circle cx="50" cy="28" r="19" fill="#E7A97B"/>
                <path d="M31 22 Q50 2 69 22 Q69 30 61 34 Q65 16 50 14 Q35 16 39 34 Q31 30 31 22 Z" fill="#2B2440"/>
                <rect x="24" y="50" width="52" height="52" rx="14" fill="var(--coral)"/>
                <rect x="40" y="66" width="20" height="26" rx="3" fill="#fff" opacity="0.9"/>
                <circle cx="42" cy="28" r="2.3" fill="#2B2440"/>
                <circle cx="58" cy="28" r="2.3" fill="#2B2440"/>
                <path d="M43 36 Q50 40 57 36" stroke="#2B2440" stroke-width="2.2" fill="none" stroke-linecap="round"/>
              </svg>
              <span>Би насанд хүрэгч</span>
            </button>
          </div>
        </div>
      </div>
      <div class="scroll-hint">
        <span>Дотор нь юу байгааг харах</span>
        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M6 9l6 6 6-6"/></svg>
      </div>
    </div>

    <div class="landing-extra">
      <div class="extra-inner">
        <h2 class="extra-title">Энд юу байдаг вэ</h2>
        <p class="extra-sub">Хоёр өөр уншигчдад зориулсан хоёр өөр зам.</p>
        <div class="feature-grid">
          <div class="feature-card pop-card">
            <div class="feature-emoji">🧭</div>
            <h3>Судлаачдад зориулсан үлгэр</h3>
            <p>Интернетээр аялах таван үе шаттай хөдөлгөөнт түүх, төгсгөлд нь товч сорил, тэмдэг хүлээж байна.</p>
          </div>
          <div class="feature-card pop-card">
            <div class="feature-emoji">📚</div>
            <h3>Насанд хүрэгчдэд зориулсан эх сурвалж</h3>
            <p>Эцэг эхчүүдийн хамгийн их асуудаг сэдвүүдийн талаар ойлгомжтой хэлээр бичсэн зөвлөмж, найдвартай байгууллагуудын холбоосууд.</p>
          </div>
          <div class="feature-card pop-card">
            <div class="feature-emoji">📝</div>
            <h3>Хоёр минутын судалгаа</h3>
            <p>Хоёр тал аль аль нь сургуулийн судалгааны төсөлд шууд ашиглагдах товч, нэрээ нууцалсан судалгаагаар төгсдөг.</p>
          </div>
        </div>
        <p class="extra-footnote">SafetyF/rst бол гэр бүлүүд онлайн аюулгүй байдлын талаар хэрхэн ярилцдагийг судалсан оюутны төсөл юм.</p>
      </div>
    </div>
  </section>

  <section id="view-kids" class="view">
    <div class="blob blob-a"></div>
    <div class="blob blob-d"></div>
    <button class="back-btn" data-target="landing">← Нүүр хуудас</button>

    <div class="view-content">
      <div class="kids-hero">
        <svg class="sticker" style="width:30px;height:30px;top:6px;left:14%;--rot:-8deg;" viewBox="0 0 24 24"><path d="M12 0 L14.5 9 L24 12 L14.5 15 L12 24 L9.5 15 L0 12 L9.5 9 Z" fill="var(--pink)"/></svg>
        <svg class="sticker" style="width:22px;height:22px;top:2px;right:16%;--rot:10deg;animation-delay:.6s;" viewBox="0 0 24 24"><circle cx="12" cy="12" r="11" fill="var(--lime)"/></svg>
        <h1><span class="marker-yellow">Судлаачийн</span> гарын авлага</h1>
        <p>Интернетээр хийх аяллыг дага, судлаач бүрийг аюулгүй байлгадаг дүрмүүдийг сур.</p>
      </div>

      <div class="story">
        <div class="tv-frame pop-card">
          <div class="tv-screen">
            <div class="stage" id="stage"></div>
          </div>
          <div class="tv-controls">
            <div class="tv-dial"></div>
            <div class="tv-speaker"><span></span><span></span><span></span><span></span><span></span><span></span></div>
            <div class="tv-dial"></div>
          </div>
        </div>
        <div class="story-controls">
          <button class="story-nav-btn btn-pop prev" id="storyPrev">← Буцах</button>
          <div class="dots" id="storyDots"></div>
          <button class="story-nav-btn btn-pop next" id="storyNext">Дараах →</button>
        </div>
      </div>

      <div class="kids-quiz" id="kidsQuiz">
        <div class="quiz-card pop-card" id="quizCard"></div>
      </div>
    </div>
  </section>

  <section id="view-adults" class="view">
    <button class="back-btn" data-target="landing">← Нүүр хуудас</button>

    <div class="view-content">
      <div class="adults-hero">
        <h1>Амьдралдаа байгаа хүүхдүүддээ интернетийг <span class="marker-coral">аюулгүй</span> судлахад нь туслаарай</h1>
        <p>Хамгийн чухал ярилцлага, тохиргооны талаарх товч бөгөөд практик зөвлөмж — мөн хүүхдийн цахим аюулгүй байдлын сургуулийн судалгааны төсөлд шууд ашиглагдах хоёр минутын судалгаа.</p>
      </div>

      <div class="callout">
        <h3>Онлайн орчны нийтлэг эрсдэлүүд, энгийн үгээр</h3>
        <ul>
          <li><strong>Фишинг:</strong> нууц үг эсвэл банкны мэдээллийг хулгайлах зорилготой хуурамч мессеж, холбоос.</li>
          <li><strong>Хортой програм:</strong> татаж авалт, холбоосоор нэвтэрдэг вирус, тагнуулын программ, эрсдэлт программ зэрэг хортой программ хангамж.</li>
          <li><strong>Нийгмийн инженерчлэл:</strong> хүүхдээс хувийн мэдээлэл гаргаж авах зорилгоор итгэл олж авах үйлдэл.</li>
          <li><strong>Цахим дээрэлхэлт:</strong> онлайнаар байнга дарамтлах, тусгаарлах, доромжлох үйлдэл.</li>
        </ul>
      </div>

      <div class="section-label">Хаанаас эхлэх вэ</div>
      <div class="resource-grid">
        <div class="resource-card pop-card">
          <h3>Танихгүй хүмүүсийн тухай ярилцах нь</h3>
          <p>Биечлэн уулзаж байгаагүй хүмүүст хувийн мэдээлэл өгөх ёсгүйг айлгалгүйгээр тайлбарлах арга, хэн нэгэн асуувал юу хийхийг зааж өгнө.</p>
          <a href="https://www.missingkids.org/netsmartz/home" target="_blank" rel="noopener">NetSmartz (NCMEC) →</a>
        </div>
        <div class="resource-card pop-card">
          <h3>Нууц үг ба бүртгэл</h3>
          <p>Хүүхдүүд өөрсдөө апп, тоглоомд бүртгүүлж эхлэхээс өмнө нас сеных нь тохирсон нууц үгийн ариун цэвэр, бүртгэлийн эзэмшлийн тухай ойлголт өгөх арга замууд.</p>
          <a href="https://www.commonsensemedia.org/" target="_blank" rel="noopener">Common Sense Media →</a>
        </div>
        <div class="resource-card pop-card">
          <h3>Дэлгэцийн цаг ба тэнцвэр</h3>
          <p>Бүх гэр бүлд адилхан тохирох цагийн хязгаар байхгүй тул өөрийн гэр бүлдээ тохирсон хязгаар тогтоох арга.</p>
          <a href="https://www.fosi.org/" target="_blank" rel="noopener">Family Online Safety Institute →</a>
        </div>
        <div class="resource-card pop-card">
          <h3>Цахим дээрэлхэлт ба онлайн эелдэг байдал</h3>
          <p>Хүүхэд бай болж байгаа (эсвэл өөр хэн нэгнийг бай болгож байгаа) шинж тэмдгийг таних, тайван хариу үйлдэл үзүүлэх арга.</p>
          <a href="https://www.nspcc.org.uk/keeping-children-safe/online-safety/" target="_blank" rel="noopener">NSPCC Online Safety →</a>
        </div>
        <div class="resource-card pop-card">
          <h3>Эцэг эхийн хяналтыг тохируулах</h3>
          <p>Утас, таблет, тоглоомын консол, Wi-Fi раутер дээрх суулгасан хяналтын тохиргоог алхам алхмаар зааж өгнө.</p>
          <a href="https://www.connectsafely.org/" target="_blank" rel="noopener">ConnectSafely →</a>
        </div>
        <div class="resource-card pop-card">
          <h3>Санаа зовоосон зүйлээ мэдээлэх</h3>
          <p>Мөлжлөг, урхидах оролдлого, хортой контентоос сэжиглэвэл хаана мэдээлэх, мэдээлсний дараа юу болохыг харуулна.</p>
          <a href="https://report.cybertip.org/" target="_blank" rel="noopener">CyberTipline →</a>
        </div>
      </div>

      <div class="further-links">
        <p class="lead">Хавчуургад нэмж болох бусад байгууллагууд:</p>
        <div class="link-row">
          <a href="https://www.pta.org/home/family-resources/safety" target="_blank" rel="noopener">National PTA — Digital Safety</a>
          <a href="https://www.thinkuknow.co.uk/parents/" target="_blank" rel="noopener">Thinkuknow (UK)</a>
          <a href="https://www.aap.org/en/patient-care/media-and-children/" target="_blank" rel="noopener">American Academy of Pediatrics</a>
          <a href="https://www.ftc.gov/business-guidance/resources/net-cetera-chatting-kids-about-being-online" target="_blank" rel="noopener">FTC — Net Cetera</a>
        </div>
      </div>

      <div class="survey">
        <div class="survey-card pop-card">
          <div class="survey-topbar"></div>
          <div class="survey-body">
            <h3>Хоёр минутын судалгаа</h3>
            <p>Нэрээ нууцалсан — таны хариулт хүүхдийн онлайн аюулгүй байдлын сургуулийн судалгааны төсөлд тусална.</p>
            <form id="adultForm">
              <div class="field">
                <label for="ageRange">Таны асарч буй хүүхдийн насны ангилал</label>
                <select id="ageRange">
                  <option>5-аас доош</option>
                  <option selected>5–8</option>
                  <option>9–12</option>
                  <option>13–17</option>
                  <option>Хамаарахгүй</option>
                </select>
              </div>
              <div class="field">
                <label>Тэдний интернет ашиглалтын талаар юу хамгийн их санаа зовоодог вэ? <span class="hint">(нэгийг сонго)</span></label>
                <div class="radio-group">
                  <label><input type="radio" name="worry" value="Strangers/contact" checked> Танихгүй хүмүүстэй холбогдох</label>
                  <label><input type="radio" name="worry" value="Screen time"> Дэлгэц хэт их харах</label>
                  <label><input type="radio" name="worry" value="Cyberbullying"> Цахим дээрэлхэлт</label>
                  <label><input type="radio" name="worry" value="Privacy/oversharing"> Хувийн мэдээллээ хэт ихээр нийтлэх</label>
                  <label><input type="radio" name="worry" value="Inappropriate content"> Зохисгүй агуулга</label>
                </div>
              </div>
              <div class="field">
                <label for="controls">Та одоогоор эцэг эхийн хяналтыг ашигладаг уу?</label>
                <select id="controls">
                  <option>Тийм</option>
                  <option>Үгүй</option>
                  <option selected>Мэдэхгүй</option>
                </select>
              </div>
              <div class="field" style="border-bottom:none;">
                <label for="topicWish">Илүү сайн ойлгохыг хүсдэг нэг сэдэв <span class="hint">(заавал биш)</span></label>
                <input type="text" id="topicWish" maxlength="120" placeholder="жишээ нь: тоглоомын чатны аюулгүй байдал">
              </div>
              <button type="submit" class="a-submit btn-pop">Хариултыг илгээх</button>
              <button type="button" class="a-export btn-pop" id="exportAdult">Хариултуудыг экспортлох</button>
            </form>
          </div>
          <div class="response-count" id="adultCount"></div>
        </div>
      </div>

      <p class="study-note">
        Энэ судалгааны хариултууд нэгдсэн мэдээллийн санд хадгалагдах бөгөөд таны төхөөрөмж дээр ч мөн нэг хуулбар хадгалагдана — доорх экспорт товч энэ орон нутгийн хуулбараас уншина.
      </p>
    </div>
  </section>

</div>

<script>
(function () {
  "use strict";

  // ---- Database config ----
  // Paste your own values here once you've created your Supabase project (see supabase-setup.sql).
  var SUPABASE_URL = 'https://tfnchxdhjxjmyxgvsxgp.supabase.co';
  var SUPABASE_ANON_KEY = 'sb_publishable_314NOowoCSKq36y2b_GQcg_hrUmHx02';

  function postToSupabase(table, payload) {
    var configured = SUPABASE_URL.indexOf('YOUR-PROJECT-REF') === -1 && SUPABASE_ANON_KEY.indexOf('YOUR-ANON-PUBLIC-KEY') === -1;
    if (!configured) return Promise.resolve(false);
    return fetch(SUPABASE_URL + '/rest/v1/' + table, {
      method: 'POST',
      headers: {
        'apikey': SUPABASE_ANON_KEY,
        'Authorization': 'Bearer ' + SUPABASE_ANON_KEY,
        'Content-Type': 'application/json',
        'Prefer': 'return=minimal'
      },
      body: JSON.stringify(payload)
    }).then(function (res) { return res.ok; }).catch(function () { return false; });
  }

  var reduceMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
  var overlay = document.getElementById('transitionOverlay');

  var views = document.querySelectorAll('.view');

  function replay(el) {
    if (!el || reduceMotion) return;
    el.classList.remove('stagger-in');
    void el.offsetWidth;
    el.classList.add('stagger-in');
  }

  function activateView(name) {
    views.forEach(function (v) { v.classList.toggle('active', v.id === 'view-' + name); });
    window.scrollTo(0, 0);
    if (history.replaceState) history.replaceState(null, '', '#' + name);
    var activeView = document.getElementById('view-' + name);
    var content = activeView.querySelector('.view-content') || activeView.querySelector('.landing-card');
    replay(content);
  }

  function showView(name) {
    if (reduceMotion) { activateView(name); return; }
    overlay.style.transition = 'none';
    overlay.style.transform = 'translateX(-100%)';
    void overlay.offsetWidth;
    overlay.style.transition = 'transform .32s cubic-bezier(.65,0,.35,1)';
    overlay.style.transform = 'translateX(0)';
    setTimeout(function () {
      activateView(name);
      overlay.style.transition = 'none';
      overlay.style.transform = 'translateX(0)';
      void overlay.offsetWidth;
      overlay.style.transition = 'transform .32s cubic-bezier(.65,0,.35,1)';
      overlay.style.transform = 'translateX(100%)';
    }, 320);
  }

  document.querySelectorAll('[data-target]').forEach(function (el) {
    el.addEventListener('click', function () { showView(el.getAttribute('data-target')); });
  });
  var initial = (location.hash || '').replace('#', '');
  if (['kids', 'adults'].indexOf(initial) !== -1) activateView(initial);

  document.getElementById('moreInfoBtn').addEventListener('click', function () {
    var el = document.getElementById('missionMore');
    var open = el.classList.toggle('open');
    this.textContent = open ? 'Хураангуйлах' : 'Дэлгэрэнгүй';
  });

  function burstConfetti() {
    if (reduceMotion) return;
    var colors = ['var(--coral)', 'var(--yellow)', 'var(--indigo)', 'var(--green)', 'var(--pink)'];
    for (var i = 0; i < 26; i++) {
      var el = document.createElement('div');
      el.className = 'confetti-piece';
      el.style.left = (Math.random() * 100) + 'vw';
      el.style.background = colors[i % colors.length];
      el.style.borderRadius = (i % 2 === 0) ? '50%' : '2px';
      el.style.animationDuration = (1.5 + Math.random() * 1.3) + 's';
      el.style.animationDelay = (Math.random() * 0.3) + 's';
      document.body.appendChild(el);
      (function (elm) { setTimeout(function () { elm.remove(); }, 3200); })(el);
    }
  }

  // Add your own image URLs here (one per slide). Leave blank to show a placeholder.
  // You can add or remove slides by adding/removing entries in this array.
  var frameImages = [
    '',
    '',
    '',
    '',
    ''
  ];
  var idx = 0;
  var stage = document.getElementById('stage');
  var dotsEl = document.getElementById('storyDots');
  var prevBtn = document.getElementById('storyPrev');
  var nextBtn = document.getElementById('storyNext');

  function renderStory(dir) {
    var src = frameImages[idx];
    var slideClass = reduceMotion ? '' : (dir === 'prev' ? 'slide-in-left' : 'slide-in-right');
    var inner = src
      ? '<img src="' + src + '" alt="Слайд ' + (idx + 1) + '">'
      : '<div class="stage-placeholder">Энд ' + (idx + 1) + '-р зургаа оруулна уу</div>';
    stage.innerHTML = '<div class="stage-inner ' + slideClass + '">' + inner + '</div>';
    dotsEl.innerHTML = frameImages.map(function (_, i) {
      return '<span class="dot' + (i === idx ? ' on' : '') + '"></span>';
    }).join('');
    prevBtn.disabled = idx === 0;
    nextBtn.textContent = idx === frameImages.length - 1 ? 'Сорил руу! →' : 'Дараах →';
  }
  prevBtn.addEventListener('click', function () { if (idx > 0) { idx--; renderStory('prev'); } });
  nextBtn.addEventListener('click', function () {
    if (idx < frameImages.length - 1) { idx++; renderStory('next'); }
    else { document.getElementById('kidsQuiz').scrollIntoView({ behavior: 'smooth' }); }
  });
  renderStory();

  var styleTag = document.createElement('style');
  styleTag.textContent = '.slide-in-right{animation:popInRight .45s cubic-bezier(.22,1,.36,1);} .slide-in-left{animation:popInLeft .45s cubic-bezier(.22,1,.36,1);}';
  document.head.appendChild(styleTag);

  var questions = [
    { q: "Тоглоомын чатад танихгүй хүн чиний сургуулийн нэрийг асуувал юу хийх вэ?", opts: ["Үнэнийг нь хэлэх", "Тоохгүй өнгөрч, том хүнд хэлэх", "Эргүүлж асуулт асуух"], correct: 1 },
    { q: "Найз чинь чамд 'туслах' гэж нууц үгээ асуувал юу хийх нь хамгийн аюулгүй вэ?", opts: ["Найз учраас хэлэх", "Найзаасаа ч гэсэн нууцалж хадгалах", "Тэдэнд бичиж өгөх"], correct: 1 },
    { q: "Гэртээ авахуулсан зургаа нийтлэхийг хүсч байна. Юуг эхлээд шалгах ёстой вэ?", opts: ["Юу ч шалгалгүй шууд нийтлэх", "Том хүнээс асууж, дэвсгэрийг шалгах", "Олон шүүлтүүр нэмэх"], correct: 1 },
    { q: "Онлайнаар хэн нэгэн чамайг эвгүй байдалд оруулах юм хэлбэл хамгийн зөв хариу үйлдэл юу вэ?", opts: ["Дотроо нуух", "Итгэдэг том хүнд хэлэх", "Хариулж, маргах"], correct: 1 },
    { q: "Нууц үгийг хүчтэй болгодог зүйл юу вэ?", opts: ["Нэр, төрсөн өдрөө ашиглах", "Зөвхөн өөрөө мэддэг үг, тооны хослол", "'password' гэдэг үгийг ашиглах"], correct: 1 }
  ];
  var qi = 0, score = 0, answered = false;
  var quizCard = document.getElementById('quizCard');

  function renderQuestion() {
    answered = false;
    var item = questions[qi];
    quizCard.innerHTML =
      '<div class="quiz-topbar"></div>'
      + '<div class="quiz-body">'
      + '<h3>' + questions.length + '-н ' + (qi + 1) + '-р асуулт</h3>'
      + '<p style="margin:0 0 0.9rem; color:var(--sub);">' + item.q + '</p>'
      + '<div class="quiz-options">' + item.opts.map(function (o, i) {
          return '<button class="quiz-opt" data-i="' + i + '">' + o + '</button>';
        }).join('') + '</div>'
      + '<div class="quiz-feedback" id="quizFeedback"></div>'
      + '</div>';

    quizCard.querySelectorAll('.quiz-opt').forEach(function (btn) {
      btn.addEventListener('click', function () {
        if (answered) return;
        answered = true;
        var choice = parseInt(btn.getAttribute('data-i'), 10);
        var correct = item.correct;
        quizCard.querySelectorAll('.quiz-opt').forEach(function (b, i) {
          if (i === correct) b.classList.add('correct');
          else if (i === choice) b.classList.add('wrong');
        });
        var fb = document.getElementById('quizFeedback');
        if (choice === correct) { score++; fb.textContent = "Тийм ээ! Энэ бол аюулгүй сонголт."; }
        else { fb.textContent = "Тийм биш ээ — аюулгүй хариултыг тодотгосон байна."; }
        setTimeout(function () {
          qi++;
          if (qi < questions.length) renderQuestion();
          else renderResult();
        }, 1300);
      });
    });
  }

  function renderResult() {
    var badge = score >= 4 ? '🌟' : score >= 2 ? '🙂' : '🌱';
    var title = score >= 4 ? 'Аюулгүй байдлын супер од!' : score >= 2 ? 'Сайн эхлэл!' : 'Үргэлжлүүлэн сур!';
    quizCard.innerHTML =
      '<div class="quiz-topbar"></div>'
      + '<div class="quiz-body">'
      + '<div class="quiz-result">'
      + '<div class="badge">' + badge + '</div>'
      + '<h3>' + title + '</h3>'
      + '<p style="color:var(--sub);">Та ' + questions.length + '-с ' + score + '-г зөв хариуллаа.</p>'
      + '</div>'
      + '<form id="kidFeedbackForm" class="kid-feedback-form">'
      + '<div>'
      + '<label>Ямар санагдсан бэ?</label>'
      + '<div class="emoji-row" id="emojiRow">'
      + ['😃', '🙂', '😐'].map(function (e) { return '<button type="button" class="emoji-choice" data-e="' + e + '">' + e + '</button>'; }).join('')
      + '</div></div>'
      + '<div><label for="kidRemember">Энэ аяллаас чиний санаж үлдэх нэг зүйл</label>'
      + '<textarea id="kidRemember" rows="2" maxlength="200" placeholder="Энд бичнэ үү..."></textarea></div>'
      + '<button type="submit" class="submit-btn btn-pop">Хариултаа илгээх</button>'
      + '<div class="thanks-note" id="kidThanks" style="display:none;">Хуваалцсанд баярлалаа!</div>'
      + '<div class="device-note">Интернетийн аюулгүй байдлын сургуулийн төсөлд илгээгдлээ — баярлалаа!</div>'
      + '</form>'
      + '</div>';

    burstConfetti();

    var pickedEmoji = '';
    document.getElementById('emojiRow').querySelectorAll('.emoji-choice').forEach(function (btn) {
      btn.addEventListener('click', function () {
        document.querySelectorAll('.emoji-choice').forEach(function (b) { b.classList.remove('picked'); });
        btn.classList.add('picked');
        pickedEmoji = btn.getAttribute('data-e');
      });
    });

    document.getElementById('kidFeedbackForm').addEventListener('submit', function (e) {
      e.preventDefault();
      var entry = {
        score: score,
        feeling: pickedEmoji,
        remembered: document.getElementById('kidRemember').value.trim(),
        at: new Date().toISOString()
      };
      try {
        var key = 'safetyfirst_kid_responses';
        var list = JSON.parse(localStorage.getItem(key) || '[]');
        list.push(entry);
        localStorage.setItem(key, JSON.stringify(list));
      } catch (err) { /* storage unavailable */ }
      postToSupabase('kid_responses', { score: entry.score, feeling: entry.feeling, remembered: entry.remembered });
      document.getElementById('kidThanks').style.display = 'block';
    });
  }
  renderQuestion();

  var ADULT_KEY = 'safetyfirst_adult_responses';
  function readAdultResponses() {
    try { return JSON.parse(localStorage.getItem(ADULT_KEY) || '[]'); } catch (e) { return []; }
  }
  function refreshAdultCount() {
    var n = readAdultResponses().length;
    document.getElementById('adultCount').textContent =
      n === 0 ? 'Энэ төхөөрөмж дээр хадгалагдсан хариулт одоогоор алга.' : 'Энэ төхөөрөмж дээр ' + n + ' хариулт хадгалагдсан байна.';
  }
  refreshAdultCount();

  document.getElementById('adultForm').addEventListener('submit', function (e) {
    e.preventDefault();
    var worry = document.querySelector('input[name="worry"]:checked');
    var entry = {
      ageRange: document.getElementById('ageRange').value,
      worry: worry ? worry.value : '',
      controls: document.getElementById('controls').value,
      topicWish: document.getElementById('topicWish').value.trim(),
      at: new Date().toISOString()
    };
    try {
      var list = readAdultResponses();
      list.push(entry);
      localStorage.setItem(ADULT_KEY, JSON.stringify(list));
    } catch (err) { /* storage unavailable */ }
    postToSupabase('adult_responses', {
      age_range: entry.ageRange,
      worry: entry.worry,
      controls: entry.controls,
      topic_wish: entry.topicWish
    });
    refreshAdultCount();
    document.getElementById('topicWish').value = '';
    var btn = e.target.querySelector('.a-submit');
    var original = btn.textContent;
    btn.textContent = 'Баярлалаа — хадгалагдлаа!';
    setTimeout(function () { btn.textContent = original; }, 1600);
  });

  document.getElementById('exportAdult').addEventListener('click', function () {
    var list = readAdultResponses();
    if (!list.length) { alert('Энэ төхөөрөмж дээр хадгалагдсан хариулт одоогоор алга.'); return; }
    var header = 'age_range,worry,uses_controls,topic_wish,submitted_at\n';
    var rows = list.map(function (r) {
      return [
        '"' + (r.ageRange || '').replace(/"/g, '""') + '"',
        '"' + (r.worry || '').replace(/"/g, '""') + '"',
        '"' + (r.controls || '').replace(/"/g, '""') + '"',
        '"' + (r.topicWish || '').replace(/"/g, '""') + '"',
        '"' + (r.at || '').replace(/"/g, '""') + '"'
      ].join(',');
    }).join('\n');
    var csv = header + rows;

    async function doExport() {
      try {
        var downloads = await window.claude.use('downloads');
        if (downloads) {
          await downloads.save({ filename: 'safetyfirst-survey-responses.csv', data: csv });
          return;
        }
      } catch (err) { /* fall through */ }
      alert('Энэ харагдацад экспорт хийх боломжгүй байна. Хариултууд:\n\n' + csv);
    }
    doExport();
  });
})();
</script>
</body>
</html>
