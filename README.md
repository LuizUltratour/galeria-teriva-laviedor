# Galeria — Teriva · La Vie d'Or

Galerias de imagens e vídeos para injeção via script no 3DVista, hospedadas no AWS S3.

**Tema:** fundo sólido `#593B31`, logo (`assets/logo.png`) no canto superior esquerdo.

- **GitHub:** https://github.com/LuizUltratour/galeria-teriva-laviedor.git
- **S3:** `s3://skylineip/Tour Virtual/teriva/galeria-la-vie-dor/`

## URLs de produção

| Arquivo | URL |
|---------|-----|
| Imagens | `https://skylineip.s3.sa-east-1.amazonaws.com/Tour+Virtual/teriva/galeria-la-vie-dor/index.html` |
| Plantas |  `https://skylineip.s3.sa-east-1.amazonaws.com/Tour+Virtual/teriva/galeria-la-vie-dor/plantas.html` |
| Vídeos  | `https://skylineip.s3.sa-east-1.amazonaws.com/Tour+Virtual/teriva/galeria-la-vie-dor/video-gallery.html` |
| Script  | `https://skylineip.s3.sa-east-1.amazonaws.com/Tour+Virtual/teriva/galeria-la-vie-dor/inject.js` |

## Estrutura

```
├── index.html          ← galeria de imagens (um sub-botão por subpasta de assets/imagens)
├── video-gallery.html  ← galeria de vídeos (lista PC ou mobile conforme o dispositivo)
├── inject.js           ← loader para o 3DVista
├── deploy.ps1          ← deploy para o S3
├── generate_thumbs.ps1 ← gera assets/thumbs e assets/large (.jpg) a partir de assets/imagens
└── assets/
    ├── imagens/<Pasta>/  ← originais (gitignored; só no PC). Cada pasta vira um botão
    ├── thumbs/imagens/   ← miniaturas 900 px (gerado)
    ├── large/imagens/    ← lightbox 2560 px (gerado)
    ├── videos/           ← vídeos de PC (gitignored; direto no S3)
    ├── videos/VERTICAIS/ ← vídeos de mobile
    ├── logo.png
    └── close-*, voltar-*, proximo-*.png
```

> O S3 é case-sensitive: os caminhos em `index.html` e `video-gallery.html` devem ter exatamente o mesmo case dos arquivos.

## Imagens

Os botões são as subpastas de `assets/imagens/` (Fotomontagem, Clube, Quadras, Praça, Portaria). Não há botão "Imagens": ao abrir já aparecem só os sub-botões. Para adicionar/renomear, edite `GALLERY_CONFIG.folders` em `index.html` e rode:

```powershell
./generate_thumbs.ps1          # só os que faltam
./generate_thumbs.ps1 -Force   # tudo
```

`.tif` também é convertido para `.jpg`.

## Vídeos

`video-gallery.html` escolhe a lista pelo dispositivo (user agent móvel ou largura ≤ 700 px):

- **PC:** `assets/videos/*.mp4` (`VIDEOS_PC`)
- **Mobile:** `assets/videos/VERTICAIS/*.mp4` (`VIDEOS_MOBILE`)

## Deploy

```powershell
./deploy.ps1            # sync completo (inclui videos) + cache-control no HTML/JS
./deploy.ps1 -QuickHtml # só index.html e inject.js
```

O sync não envia `assets/imagens/` (originais).

## Integração 3DVista

```js
(function(){
  var s = document.createElement('script');
  s.src = 'https://skylineip.s3.sa-east-1.amazonaws.com/Tour+Virtual/teriva/galeria-la-vie-dor/inject.js?v=' + Date.now();
  document.head.appendChild(s);
})();
```

```js
GaleriaImagens(1);     // abre galeria de imagens   (0 fecha)
GaleriaPlantas(1);     // abre as plantas em tela cheia (0 fecha)
AbrirGaleriaVideos(1); // abre galeria de vídeos    (0 fecha)
```
