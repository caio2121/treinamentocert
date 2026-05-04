# HCIA Storage exam simulator

Static Huawei HCIA Storage V5.0 practice exam (single HTML app).

## GitHub Pages

After the first successful deploy, the simulator is available at:

**https://caio2121.github.io/treinamentocert/**

The site is served from the `docs/` folder on branch `main` (`docs/index.html`).

### Enable Pages (one-time on GitHub)

1. Open the repository on GitHub: `https://github.com/caio2121/treinamentocert`
2. Go to **Settings** → **Pages**
3. Under **Build and deployment** → **Source**, choose **Deploy from a branch**
4. Set **Branch** to `main` and folder **`/docs`**, then **Save**
5. Wait one or two minutes and open the URL above

### Local preview

Serve the repo root with any static server and open `docs/index.html`, for example:

```bash
python -m http.server 8765
```

Then visit `http://127.0.0.1:8765/docs/`.
