// BASH LAB v3 — движение. GSAP 3.13 (ScrollTrigger, SplitText) + Lenis. Без движения (reduced) всё видно и работает.
(() => {
  const H = document.documentElement;
  const reduced = H.classList.contains('reduced');
  const desktop = matchMedia('(min-width: 821px)').matches;
  const fine = matchMedia('(hover: hover) and (pointer: fine)').matches;
  const $ = (s, r = document) => r.querySelector(s);
  const $$ = (s, r = document) => [...r.querySelectorAll(s)];
  const store = (k, v) => { try { v === undefined ? sessionStorage.removeItem(k) : sessionStorage.setItem(k, v); } catch (e) {} };

  // ролики: грузим, когда подходят к экрану, играем только видимые
  const vio = new IntersectionObserver((es) => es.forEach(({ target: v, isIntersecting: on }) => {
    if (on) {
      if (v.dataset.src) { v.src = v.dataset.src; v.removeAttribute('data-src'); }
      v.play().catch(() => {});
    } else if (!v.paused) v.pause();
  }), { rootMargin: '300px 0px' });
  $$('video').forEach((v) => vio.observe(v));

  // заявки для рекламы: клик «Онлайн-запись» и WhatsApp → dataLayer (GTM/Google Ads берут отсюда конверсии);
  // метки объявления (utm_*, gclid) из адреса страницы уходят в ссылку онлайн-записи
  window.dataLayer = window.dataLayer || [];
  const q = new URLSearchParams(location.search), tags = [...q].filter(([k]) => /^(utm_|gclid|gbraid|wbraid)/.test(k));
  $$('a[href*="alteg.io"]').forEach((a) => { if (tags.length) { const u = new URL(a.href); tags.forEach(([k, v]) => u.searchParams.set(k, v)); a.href = u; } });
  document.addEventListener('click', (ev) => {
    const a = ev.target.closest('a[href*="alteg.io"], a[href*="wa.me"]');
    if (!a) return;
    const wa = a.href.includes('wa.me');
    window.dataLayer.push({ event: wa ? 'whatsapp_click' : 'book_online_click', page: document.body.dataset.page, lang: H.lang,
      item: wa ? decodeURIComponent((a.href.split('text=')[1] || '')).slice(0, 120) : '', place: (a.closest('section, header, footer, .bar') || {}).className || '' });
  }, true);

  // меню на телефоне
  $('.burger').addEventListener('click', () => {
    const open = H.classList.toggle('menu-open');
    $('.menu').setAttribute('aria-hidden', String(!open));
  });

  // вкладки прайса
  $$('.tabs button').forEach((b) => b.addEventListener('click', () => {
    $$('.tabs button').forEach((x) => x.setAttribute('aria-selected', String(x === b)));
    $$('.pc').forEach((c) => { c.hidden = c.dataset.sec !== b.dataset.sec; });
    const card = $('.pc[data-sec="' + b.dataset.sec + '"]');
    if (window.gsap && !reduced) gsap.from(card.querySelectorAll('.row'), { y: 18, opacity: 0, duration: .6, stagger: .035, ease: 'power3.out' });
    window.ScrollTrigger && ScrollTrigger.refresh();
  }));

  // бегущие строки: копия содержимого для бесшовного круга
  $$('.tk').forEach((t) => { t.innerHTML += t.innerHTML; });

  if (!window.gsap || reduced) { $('.loader') && $('.loader').remove(); H.classList.remove('wiping'); return; }
  gsap.registerPlugin(ScrollTrigger, SplitText);

  // плавная прокрутка
  const lenis = new Lenis({ lerp: .09, smoothWheel: true });
  lenis.on('scroll', ScrollTrigger.update);
  gsap.ticker.add((t) => lenis.raf(t * 1000));
  gsap.ticker.lagSmoothing(0);
  $$('a[href^="#"]').forEach((a) => a.addEventListener('click', (ev) => { ev.preventDefault(); lenis.scrollTo(a.getAttribute('href'), { offset: -70 }); }));

  const E = 'expo.out';

  // заголовки строками из-под маски
  const splits = $$('.split').map((el) => new SplitText(el, { type: 'lines', linesClass: 'ln', mask: 'lines' }));
  splits.forEach((s) => gsap.from(s.lines, { yPercent: 105, duration: 1.3, stagger: .09, ease: E,
    scrollTrigger: { trigger: s.lines[0].closest('section') ? s.lines[0] : s.lines[0], start: 'top 88%' } }));

  // ── первый экран: загрузка → шторка → строки
  const h1 = new SplitText('.h1', { type: 'lines', linesClass: 'ln', mask: 'lines' });
  const intro = gsap.timeline({ paused: true, defaults: { ease: E } });
  // на телефоне кадр виден сразу (быстрая первая отрисовка для рекламы), шторка — только на компьютере
  if (desktop) intro.from('.hp', { clipPath: 'inset(100% 0 0 0)', duration: 1.5, stagger: .12 });
  intro.from(desktop ? '.hp .hv' : '.hero-m .hv', { scale: desktop ? 1.25 : 1.08, duration: 2.2 }, 0)
    .from(h1.lines, { yPercent: 110, duration: 1.3, stagger: .1 }, '-=1.3')
    .from('.hero .eyebrow, .hero .lead, .hero-cta > *', { y: 24, opacity: 0, duration: 1, stagger: .06 }, '-=1')
    .from('.badge', { scale: 0, rotate: -40, duration: 1.1, ease: 'back.out(1.6)' }, '-=.9')
    .from('.hd > *', { y: -30, opacity: 0, duration: 1, stagger: .08 }, '-=1.1');

  const loader = $('.loader');
  if (loader && !H.classList.contains('seen') && !H.classList.contains('wiping')) {
    store('bl-seen', '1');
    lenis.stop();
    const word = new SplitText('.ld-word', { type: 'chars' });
    gsap.timeline({ defaults: { ease: E } })
      .from('.ld-mark', { opacity: 0, scale: .8, duration: .6 })
      .from(word.chars, { opacity: 0, y: 12, duration: .6, stagger: .03 }, '-=.45')
      .to('.ld-line i', { scaleX: 1, duration: .7, ease: 'power2.inOut' }, '-=.5')
      .to(loader, { clipPath: 'inset(0 0 100% 0)', duration: .9, ease: 'expo.inOut' })
      .add(() => { loader.remove(); lenis.start(); intro.play(); }, '-=.5');
  } else {
    loader && loader.remove();
    store('bl-seen', '1');
    if (H.classList.contains('wiping')) {
      store('bl-wipe');
      gsap.to('.wipe', { yPercent: -100, duration: 1, ease: 'expo.inOut', delay: .1, onComplete: () => { H.classList.remove('wiping'); gsap.set('.wipe', { clearProps: 'all' }); } });
      intro.delay(.35);
    }
    intro.play();
  }

  // переходы между страницами — розовая шторка
  $$('a[href$=".html"]').forEach((a) => a.addEventListener('click', (ev) => {
    if (a.target === '_blank' || ev.metaKey || ev.ctrlKey) return;
    ev.preventDefault();
    store('bl-wipe', '1');
    gsap.fromTo('.wipe', { yPercent: 100 }, { yPercent: 0, duration: .8, ease: 'expo.inOut', onComplete: () => { location.href = a.href; } });
  }));
  addEventListener('pageshow', (ev) => { if (ev.persisted) gsap.set('.wipe', { yPercent: 100 }); });

  // шапка: прозрачная над героем, дальше плотная; прячется вниз-по-скроллу
  const hd = $('.hd');
  ScrollTrigger.create({ start: () => innerHeight - 90, end: 'max',
    onToggle: (s) => { hd.classList.toggle('solid', s.isActive); $('.bar') && $('.bar').classList.toggle('on', s.isActive); },
    onUpdate: (s) => hd.classList.toggle('up', s.isActive && s.direction === 1 && s.getVelocity() > 40 && !H.classList.contains('menu-open')) });

  // герой на прокрутке: панели расходятся по высоте, текст уходит
  const ht = { trigger: '.hero', start: 'top top', end: 'bottom top', scrub: true };
  gsap.to('.hp0 .hv', { yPercent: 9, ease: 'none', scrollTrigger: ht });
  gsap.to('.hp1 .hv', { yPercent: -6, ease: 'none', scrollTrigger: ht });
  gsap.to('.hp2 .hv', { yPercent: 12, ease: 'none', scrollTrigger: ht });
  gsap.to('.hero-txt', { yPercent: -30, opacity: 0, ease: 'none', scrollTrigger: { ...ht, end: '70% top' } });
  gsap.to('.badge', { rotate: 50, ease: 'none', scrollTrigger: ht });

  // бегущая строка: ускоряется и наклоняется от скорости прокрутки
  $$('.tk').forEach((t) => {
    const loop = gsap.to(t, { xPercent: -50, duration: 38, ease: 'none', repeat: -1 });
    const skew = gsap.quickTo(t, 'skewX', { duration: .5, ease: 'power3' });
    ScrollTrigger.create({ trigger: t, start: 'top bottom', end: 'bottom top', onUpdate: (s) => {
      const v = s.getVelocity();
      loop.timeScale(1 + Math.min(Math.abs(v) / 400, 5) * (v < 0 ? -1 : 1));
      skew(gsap.utils.clamp(-8, 8, v / -300));
    }, onLeave: () => { loop.timeScale(1); skew(0); }, onLeaveBack: () => { loop.timeScale(1); skew(0); } });
  });

  // манифест: слова проявляются по прокрутке
  $$('[data-words]').forEach((el) => {
    const s = new SplitText(el, { type: 'words', wordsClass: 'w', aria: 'none' });
    gsap.to(s.words, { opacity: 1, stagger: .1, ease: 'none', scrollTrigger: { trigger: el, start: 'top 78%', end: 'bottom 52%', scrub: .6 } });
  });
  gsap.from('.facts li', { y: 40, opacity: 0, duration: 1.1, stagger: .12, ease: E, scrollTrigger: { trigger: '.facts', start: 'top 85%' } });

  // закреплённые сцены создаём строго сверху вниз: иначе нижняя считает старт без отступа верхней и наезжает на неё
  const SCENES = {
    // утро в салоне: прокрутка листает их рилс по кадрам
    morning(mo) {
      const cv = $('.mo-c', mo), cx = cv.getContext('2d'), N = 115, frames = [], steps = $$('li', mo);
      const at = [0, 18, 51, 83];
      const base = $('link[rel=icon]').href.replace(/img\/bl\.svg$/, 'seq/');
      let loaded = false, cur = -1;
      const draw = (i) => { const f = frames[i]; if (f && f.complete && f.naturalWidth) { cx.drawImage(f, 0, 0, cv.width, cv.height); cur = i; } };
      const load = () => { if (loaded) return; loaded = true;
        for (let i = 0; i < N; i++) { const im = new Image(); im.src = base + 'f' + String(i + 1).padStart(3, '0') + '.webp'; if (i === 0) im.onload = () => draw(0); frames.push(im); } };
      ScrollTrigger.create({ trigger: mo, start: 'top 250%', onEnter: load });
      const st = { i: 0 };
      gsap.to(st, { i: N - 1, ease: 'none', snap: 'i',
        scrollTrigger: { trigger: mo, start: 'top top', end: '+=300%', scrub: .5, pin: desktop ? $('.mo-in', mo) : false },
        onUpdate: () => {
          const i = Math.round(st.i);
          if (i !== cur) draw(i);
          let k = 0; at.forEach((a, j) => { if (i >= a) k = j; });
          steps.forEach((s, j) => s.classList.toggle('on', j === k));
        } });
    },
    // «руки»: мастера зажигаются по одному, счётчик 1 → 2 → 4 → 6 → 10 (и на телефоне)
    hands(hn) {
      const steps = $$('.hn-steps li', hn), n = steps.length;
      const rollH = () => $('.hn-roll span', hn).offsetHeight, wordH = () => $('.hn-wroll span', hn).offsetHeight;
      const tl = gsap.timeline({ defaults: { ease: 'power2.inOut', duration: 1 },
        scrollTrigger: { trigger: hn, start: 'top top', end: '+=' + (n * 70) + '%', scrub: .8, pin: $('.hn-in', hn), invalidateOnRefresh: true } });
      tl.fromTo($('.hnv', hn), { scale: 1.12 }, { scale: 1, duration: n - 1, ease: 'none' }, 0);
      steps.forEach((li, k) => {
        if (!k) return;
        const at = k - 1;
        li.dataset.new.split(' ').forEach((h) => {
          const el = hn.querySelector('ellipse[data-h="' + h + '"]');
          tl.to(el, { attr: { rx: +el.dataset.rx, ry: +el.dataset.ry } }, at);
          tl.fromTo(hn.querySelector('.hn-lb[data-h="' + h + '"]'), { opacity: 0, y: 10 }, { opacity: 1, y: 0, duration: .5 }, at + .4);
        });
        tl.to($('.hn-roll', hn), { y: () => -rollH() * k }, at)
          .to($('.hn-wroll', hn), { y: () => -wordH() * k }, at)
          .to(steps[k - 1], { opacity: 0, y: -20, duration: .4 }, at)
          .fromTo(li, { opacity: 0, y: 20 }, { opacity: 1, y: 0, duration: .5 }, at + .45);
      });
    },
    // маленький кадр раскрывается, подписи расходятся (только компьютер)
    film(f) {
      if (!desktop) return;
      const land = f.classList.contains('film-land');
      const tl = gsap.timeline({ scrollTrigger: { trigger: f, start: 'top top', end: '+=180%', scrub: 1, pin: $('.film-in', f) } });
      // широкий исходник 1080 px — раскрываем до рамки, а не во весь экран, чтобы не мылить
      tl.fromTo($('.fm', f), { clipPath: land ? 'inset(32% 38% 32% 38% round 22px)' : 'inset(20% 22% 20% 22% round 22px)' },
        { clipPath: land ? 'inset(9% 15% 9% 15% round 18px)' : 'inset(0% 0% 0% 0% round 0px)', ease: 'power2.inOut' })
        .fromTo($('.fv', f), { scale: 1.3 }, { scale: 1, ease: 'power2.inOut' }, 0)
        .fromTo($('.fl', f), { x: 0 }, { x: '-18vw', ease: 'power2.inOut' }, 0)
        .fromTo($('.fr', f), { x: 0 }, { x: '18vw', ease: 'power2.inOut' }, 0)
        .to($$('.fl, .fr', f), { opacity: 0, duration: .25 }, .75);
    },
    // стена работ: горизонтальный проезд, внутри кадров — параллакс (только компьютер)
    wall(wall) {
      if (!desktop) return;
      const track = $('.wall-track', wall);
      const dist = () => track.scrollWidth - innerWidth;
      const ride = gsap.to(track, { x: () => -dist(), ease: 'none',
        scrollTrigger: { trigger: wall, start: 'top top', end: () => '+=' + dist(), scrub: 1, pin: true, invalidateOnRefresh: true } });
      $$('.wm', track).forEach((m) => gsap.fromTo(m, { xPercent: -6 }, { xPercent: 6, ease: 'none',
        scrollTrigger: { trigger: m.parentNode, containerAnimation: ride, start: 'left right', end: 'right left', scrub: true } }));
    },
  };
  $$('[data-morning],[data-hands],[data-film],[data-wall]').forEach((el) => {
    SCENES[['morning', 'hands', 'film', 'wall'].find((k) => el.hasAttribute('data-' + k))](el);
  });

  if (desktop) {
    // салон: фото плывут медленнее страницы
    $$('.sg img').forEach((im, i) => gsap.fromTo(im, { yPercent: -8 }, { yPercent: 8 + i * 2, ease: 'none',
      scrollTrigger: { trigger: im.parentNode, start: 'top bottom', end: 'bottom top', scrub: true } }));
    gsap.from('.salon-v', { clipPath: 'inset(100% 0 0 0 round 999px 999px 0 0)', duration: 1.6, ease: E, scrollTrigger: { trigger: '.salon', start: 'top 70%' } });
  }

  // подвал: огромное слово поднимается по буквам
  const fw = new SplitText('.ft-word', { type: 'chars', charsClass: 'ch' });
  gsap.from(fw.chars, { yPercent: 100, opacity: 0, duration: 1.2, stagger: .05, ease: E, scrollTrigger: { trigger: '.ft', start: 'top 85%' } });

  // курсор: точка, над кнопками — круг с подписью
  if (fine) {
    const c = $('.cursor'), dot = $('span', c);
    const qx = gsap.quickTo(c, 'x', { duration: .45, ease: 'power3' }), qy = gsap.quickTo(c, 'y', { duration: .45, ease: 'power3' });
    addEventListener('mousemove', (e) => { qx(e.clientX); qy(e.clientY); });
    $$('[data-cursor]').forEach((el) => {
      el.addEventListener('mouseenter', () => { c.classList.add('big'); dot.textContent = el.dataset.cursor; });
      el.addEventListener('mouseleave', () => { c.classList.remove('big'); dot.textContent = ''; });
    });
    document.addEventListener('mouseleave', () => c.classList.add('hide'));
    document.addEventListener('mouseenter', () => c.classList.remove('hide'));
    // кнопки чуть тянутся к курсору
    $$('.btn').forEach((b) => {
      const bx = gsap.quickTo(b, 'x', { duration: .6, ease: 'elastic.out(1,.4)' }), by = gsap.quickTo(b, 'y', { duration: .6, ease: 'elastic.out(1,.4)' });
      b.addEventListener('mousemove', (e) => { const r = b.getBoundingClientRect(); bx((e.clientX - r.left - r.width / 2) * .25); by((e.clientY - r.top - r.height / 2) * .35); });
      b.addEventListener('mouseleave', () => { bx(0); by(0); });
    });
  }

  // WebGL: кадр под курсором идёт волной и расслаивается по цвету в сторону движения. Один холст на всю страницу,
  // переезжает в карточку под курсором. С file:// браузер не даёт текстуру (защита) — тогда эффекта просто нет.
  if (fine && desktop) (() => {
    const cv = document.createElement('canvas');
    cv.className = 'fx';
    const gl = cv.getContext('webgl', { alpha: false, antialias: false });
    if (!gl) return;
    const sh = (type, src) => { const o = gl.createShader(type); gl.shaderSource(o, src); gl.compileShader(o); return o; };
    const pr = gl.createProgram();
    gl.attachShader(pr, sh(gl.VERTEX_SHADER, 'attribute vec2 p;varying vec2 v;void main(){v=p*.5+.5;v.y=1.-v.y;gl_Position=vec4(p,0.,1.);}'));
    gl.attachShader(pr, sh(gl.FRAGMENT_SHADER, [
      'precision mediump float;varying vec2 v;uniform sampler2D t;uniform vec2 m,vel,card,cover;uniform vec4 box;uniform float s,time;',
      'vec3 tex(vec2 q){vec2 iv=(q*card-box.xy)/box.zw;vec2 tv=(iv-.5)*cover+.5;return texture2D(t,tv).rgb;}',
      'void main(){vec2 q=v;vec2 d=(q-m)*vec2(card.x/card.y,1.);float r=length(d);',
      'float w=sin(r*34.-time*4.5)*exp(-r*6.)*.024*s;q+=normalize(d+1e-5)/vec2(card.x/card.y,1.)*w;q-=vel*.05*s*exp(-r*3.);',
      'vec2 c=vel*.02*s+normalize(d+1e-5)*.0025*s*exp(-r*5.);gl_FragColor=vec4(tex(q+c).r,tex(q).g,tex(q-c).b,1.);}'].join('')));
    gl.linkProgram(pr); gl.useProgram(pr);
    const buf = gl.createBuffer(); gl.bindBuffer(gl.ARRAY_BUFFER, buf);
    gl.bufferData(gl.ARRAY_BUFFER, new Float32Array([-1, -1, 1, -1, -1, 1, 1, 1]), gl.STATIC_DRAW);
    const loc = gl.getAttribLocation(pr, 'p'); gl.enableVertexAttribArray(loc); gl.vertexAttribPointer(loc, 2, gl.FLOAT, false, 0, 0);
    const U = {}; ['m', 'vel', 'card', 'cover', 'box', 's', 'time'].forEach((k) => { U[k] = gl.getUniformLocation(pr, k); });
    const tx = gl.createTexture(); gl.bindTexture(gl.TEXTURE_2D, tx);
    [gl.TEXTURE_WRAP_S, gl.TEXTURE_WRAP_T].forEach((k) => gl.texParameteri(gl.TEXTURE_2D, k, gl.CLAMP_TO_EDGE));
    gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MIN_FILTER, gl.LINEAR);
    let card = null, src = null, ok = true, run = false;
    const st = { s: 0 }, mo = { x: .5, y: .5 }, sm = { x: .5, y: .5, vx: 0, vy: 0 };
    const upload = () => { try { gl.texImage2D(gl.TEXTURE_2D, 0, gl.RGB, gl.RGB, gl.UNSIGNED_BYTE, src); return true; } catch (e) { ok = false; return false; } };
    const frame = (time) => {
      if (!card) { run = false; return; }
      const r = card.getBoundingClientRect(), ir = src.getBoundingClientRect(), dpr = Math.min(devicePixelRatio, 1.5);
      if (cv.width !== Math.round(r.width * dpr)) { cv.width = Math.round(r.width * dpr); cv.height = Math.round(r.height * dpr); gl.viewport(0, 0, cv.width, cv.height); }
      const nw = src.videoWidth || src.naturalWidth, nh = src.videoHeight || src.naturalHeight, ab = ir.width / ir.height, at = nw / nh;
      if (src.tagName === 'VIDEO' && !upload()) return;
      const px = sm.x, py = sm.y; sm.x += (mo.x - sm.x) * .12; sm.y += (mo.y - sm.y) * .12;
      sm.vx += ((sm.x - px) * 8 - sm.vx) * .2; sm.vy += ((sm.y - py) * 8 - sm.vy) * .2;
      gl.uniform2f(U.m, sm.x, sm.y); gl.uniform2f(U.vel, sm.vx, sm.vy); gl.uniform2f(U.card, r.width, r.height);
      gl.uniform4f(U.box, ir.left - r.left, ir.top - r.top, ir.width, ir.height);
      gl.uniform2f(U.cover, at > ab ? ab / at : 1, at > ab ? 1 : at / ab);
      gl.uniform1f(U.s, st.s); gl.uniform1f(U.time, time / 1000);
      gl.drawArrays(gl.TRIANGLE_STRIP, 0, 4);
      requestAnimationFrame(frame);
    };
    $$('.wc, .others a').forEach((el) => {
      const media = el.querySelector('img, video');
      if (!media) return;
      el.addEventListener('mouseenter', (e) => {
        if (!ok || (media.tagName === 'VIDEO' ? media.readyState < 2 : !media.complete)) return;
        card = el; src = media;
        if (!upload()) { card = null; return; }
        const r = el.getBoundingClientRect(); mo.x = sm.x = (e.clientX - r.left) / r.width; mo.y = sm.y = (e.clientY - r.top) / r.height; sm.vx = sm.vy = 0;
        el.appendChild(cv);
        gsap.to(st, { s: 1, duration: .8, ease: 'power2.out', overwrite: true });
        if (!run) { run = true; requestAnimationFrame(frame); }
      });
      el.addEventListener('mousemove', (e) => { if (card !== el) return; const r = el.getBoundingClientRect(); mo.x = (e.clientX - r.left) / r.width; mo.y = (e.clientY - r.top) / r.height; });
      el.addEventListener('mouseleave', () => {
        if (card !== el) return;
        gsap.to(st, { s: 0, duration: .6, ease: 'power2.out', overwrite: true, onComplete: () => { if (card === el) { card = null; cv.remove(); } } });
      });
    });
  })();

  addEventListener('load', () => ScrollTrigger.refresh());
})();
