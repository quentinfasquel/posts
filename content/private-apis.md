+++
title = "The Wonderful World of Private APIs"
slug = "private-apis"
+++

<style>
.projects-grid {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 2rem;
  margin-top: 2rem;
  
  /* Full-bleed: break out of container */
  width: 100vw;
  position: relative;
  left: 50%;
  margin-left: -50vw;
  padding: 2rem 4rem;
  box-sizing: border-box;
}

.project-card {
  flex: 0 1 320px;
  max-width: 400px;
  padding: 1.5rem;
  text-align: center;
  display: flex;
  flex-direction: column;
}

@media only screen and (max-width: 600px) {
  .projects-grid {
    padding: 1.5rem;
  }
  
  .project-card {
    flex: 1 1 100%;
    max-width: 100%;
  }
}

.project-icon {
  width: 100px;
  height: 100px;
  margin: 0 auto 1rem;
  display: flex;
  align-items: center;
  justify-content: center;
}

.project-icon img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 22px;
}

.project-icon img[src*="private-symbols"] {
  transform: scale(1.3);
}

.project-title {
  font-size: 2.2rem;
  font-weight: 700;
  margin-bottom: 0.5rem;
}

.project-title a {
  color: inherit !important;
  text-decoration: none;
  transition: none;
}

.project-title a:hover,
.project-title a:focus {
  color: #1565c0 !important;
}

/* Dark mode hover */
body.colorscheme-dark .project-title a:hover,
body.colorscheme-dark .project-title a:focus {
  color: #42a5f5 !important;
}

@media (prefers-color-scheme: dark) {
  body.colorscheme-auto .project-title a:hover,
  body.colorscheme-auto .project-title a:focus {
    color: #42a5f5 !important;
  }
}

.project-description {
  flex: 1;
  font-size: 1.6rem;
  line-height: 1.6;
  margin-bottom: 1rem;
}

.project-link {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  margin-top: 1rem;
  font-size: 1.4rem;
}

.project-link:hover {
  text-decoration: underline;
}

/* Dark mode adjustments */
@media (prefers-color-scheme: dark) {
  .project-card {
    /* background: #2a2a2a; */
  }
}
</style>

<div class="projects-grid">

<div class="project-card">
  <div class="project-icon">
    <img src="/images/projects/private-symbols.png" alt="PrivateSymbols Icon">
  </div>
  <h3 class="project-title">
    <a href="https://github.com/quentinfasquel/PrivateSymbols">PrivateSymbols</a>
  </h3>
  <p class="project-description">
    A macOS app like SF Symbols that lets you browse private SF Symbols. Download the app and explore Apple's hidden symbol library.
  </p>
  <a href="https://github.com/quentinfasquel/PrivateSymbols" class="project-link">
    <i class="fa-brands fa-github"></i> View on GitHub
  </a>
</div>

<div class="project-card">
  <div class="project-icon">
    <img src="/images/projects/cafilterbuiltins.svg" alt="CAFilterBuiltins Icon">
  </div>
  <h3 class="project-title">
    <a href="https://github.com/quentinfasquel/CAFilterBuiltins">CAFilterBuiltins</a>
  </h3>
  <p class="project-description">
    A Swift package providing a CIFilterBuiltins-like API for Core Animation's private CAFilter class. Type-safe, Swift-friendly filters.
  </p>
  <a href="https://github.com/quentinfasquel/CAFilterBuiltins" class="project-link">
    <i class="fa-brands fa-github"></i> View on GitHub
  </a>
</div>

<div class="project-card">
  <div class="project-icon">
    <img src="/images/projects/quartzmore.svg" alt="QuartzMore Icon">
  </div>
  <h3 class="project-title">
    <a href="https://github.com/quentinfasquel/QuartzMore">QuartzMore</a>
  </h3>
  <p class="project-description">
    <i>"More from QuartzCore"</i><br/>
    An experimental Swift package that provides lightweight, type-safe wrappers around Core Animation private APIs.
  </p>
  <a href="https://github.com/quentinfasquel/QuartzMore" class="project-link">
    <i class="fa-brands fa-github"></i> View on GitHub
  </a>
</div>

</div>
