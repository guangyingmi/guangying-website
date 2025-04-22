<!-- 现代背景 -->
![color](#fafafa)

<!-- 现代化容器设计 -->
<div style="display: flex; flex-direction: column; align-items: center; justify-content: center; min-height: 100vh; padding: 2rem; position: relative; font-family: 'Montserrat', sans-serif;">
  
  <!-- 现代几何装饰元素 -->
  <div style="position: absolute; width: 100%; height: 100%; z-index: -1; overflow: hidden;">
    <!-- 左上装饰 -->
    <div style="position: absolute; top: -5%; left: -5%; width: 30%; height: 30%; border-radius: 0 0 50% 0; background: rgba(85, 239, 196, 0.06);"></div>
    <!-- 右下装饰 -->
    <div style="position: absolute; bottom: -5%; right: -5%; width: 30%; height: 30%; border-radius: 50% 0 0 0; background: rgba(85, 239, 196, 0.06);"></div>
    <!-- 线条装饰 -->
    <div style="position: absolute; top: 10%; right: 10%; width: 150px; height: 1px; background: #e2e2e2; transform: rotate(45deg);"></div>
    <div style="position: absolute; bottom: 10%; left: 10%; width: 150px; height: 1px; background: #e2e2e2; transform: rotate(45deg);"></div>
  </div>

  
  <!-- 现代标题设计 -->
  <div style="position: relative; text-align: center; margin-bottom: 0.5rem;">
    <h1 style="font-size: 3.2rem; font-weight: 800; color: #2d3436; letter-spacing: -1px; margin: 0; position: relative;">
      <span style="color: #55efc4;">Guang</span>Ying's Blog
    </h1>
    <div style="position: absolute; top: 0; right: -15px; width: 8px; height: 8px; background: #55efc4; border-radius: 50%;"></div>
  </div>
  
  <!-- 简约副标题 -->
  <div style="height: 2px; width: 40px; background: #55efc4; margin: 1.5rem 0;"></div>
  
  <!-- 标语 - 现代排版 -->
  <p style="font-size: 1rem; max-width: 500px; text-align: center; margin-bottom: 3rem; color: #636e72; font-weight: 400; line-height: 1.8; letter-spacing: 0.2px;">
    读书是世界上门槛最低的高贵举动。在阅读上花的每一秒，都将沉淀更好的你。
  </p>
  
  <!-- 现代化数据展示 -->
  <div style="display: flex; justify-content: center; gap: 3.5rem; margin-bottom: 3.5rem;">
    <!-- 阅读量 -->
    <div style="display: flex; flex-direction: column; align-items: center; position: relative;">
      <div id="busuanzi_container_site_pv">
        <span id="busuanzi_value_site_pv" style="font-size: 2.2rem; font-weight: 700; color: #2d3436; font-feature-settings: 'tnum'; letter-spacing: -1px;"></span>
      </div>
      <div style="font-size: 0.8rem; color: #636e72; text-transform: uppercase; letter-spacing: 1px; margin-top: 0.3rem;">阅读量</div>
      <div style="position: absolute; bottom: -10px; left: 50%; transform: translateX(-50%); width: 20px; height: 2px; background: #55efc4;"></div>
    </div>
    
    <!-- 访客数 -->
    <div style="display: flex; flex-direction: column; align-items: center; position: relative;">
      <div id="busuanzi_container_site_uv">
        <span id="busuanzi_value_site_uv" style="font-size: 2.2rem; font-weight: 700; color: #2d3436; font-feature-settings: 'tnum'; letter-spacing: -1px;"></span>
      </div>
      <div style="font-size: 0.8rem; color: #636e72; text-transform: uppercase; letter-spacing: 1px; margin-top: 0.3rem;">访客数</div>
      <div style="position: absolute; bottom: -10px; left: 50%; transform: translateX(-50%); width: 20px; height: 2px; background: #55efc4;"></div>
    </div>
  </div>
  
  <!-- 现代按钮设计 -->
  <div style="display: flex; gap: 1.2rem;">
    <a href="#webReadMe" style="text-decoration: none; padding: 0.8rem 2rem; background: #2d3436; color: white; border-radius: 3px; font-weight: 600; font-size: 0.95rem; letter-spacing: 0.5px; transition: all 0.2s; position: relative; overflow: hidden;">
      <span style="position: relative; z-index: 1;">开始阅读</span>
      <div style="position: absolute; top: 0; left: 0; width: 3px; height: 100%; background: #55efc4; transition: all 0.3s;"></div>
    </a>
    
    <a href="https://github.com/guangyingmi/guangying-website" style="text-decoration: none; padding: 0.8rem 2rem; background: transparent; color: #2d3436; border: 1px solid #e4e4e4; border-radius: 3px; font-weight: 600; font-size: 0.95rem; letter-spacing: 0.5px; transition: all 0.2s; display: flex; align-items: center;">
      <i class="fab fa-github" style="margin-right: 0.7rem;"></i>GitHub
    </a>
  </div>
</div>

<!-- 加载资源 -->
<script>
  document.addEventListener('DOMContentLoaded', function() {
    // 添加Font Awesome
    const fontAwesome = document.createElement('link');
    fontAwesome.rel = 'stylesheet';
    fontAwesome.href = 'https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.4/css/all.min.css';
    document.head.appendChild(fontAwesome);
    
    // 添加Montserrat字体
    const montserratFont = document.createElement('link');
    montserratFont.rel = 'stylesheet';
    montserratFont.href = 'https://fonts.googleapis.com/css2?family=Montserrat:wght@400;500;600;700;800&display=swap';
    document.head.appendChild(montserratFont);
    
    // 应用Montserrat字体
    document.body.style.fontFamily = "'Montserrat', sans-serif";
    
    // 添加阅读按钮悬停效果
    const mainButton = document.querySelector('a[style*="background: #2d3436"]');
    if (mainButton) {
      mainButton.addEventListener('mouseenter', () => {
        const indicator = mainButton.querySelector('div');
        indicator.style.width = '100%';
      });
      
      mainButton.addEventListener('mouseleave', () => {
        const indicator = mainButton.querySelector('div');
        indicator.style.width = '3px';
      });
    }
    
    // 添加GitHub按钮悬停效果
    const githubButton = document.querySelector('a[style*="border: 1px solid #e4e4e4"]');
    if (githubButton) {
      githubButton.addEventListener('mouseenter', () => {
        githubButton.style.backgroundColor = '#f8f8f8';
      });
      
      githubButton.addEventListener('mouseleave', () => {
        githubButton.style.backgroundColor = 'transparent';
      });
    }
  });
</script>
