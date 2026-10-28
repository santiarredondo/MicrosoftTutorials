# Santiago's Microsoft Tutorials

Dedicated site and repository hosting Azure labs, Microsoft Copilot Studio research, Intune, Entra, Defender, and all things Microsoft. Created to help others with labs, tutorials, blogs, and findings.

**Live site (after GitHub Pages is enabled):** <https://santiarredondo.github.io/MicrosoftTutorials/>

## Preview locally

This project uses [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) (Jekyll 4). macOS system Ruby is too old; use the Dev Container or Docker.

```bash
# From the repo root
docker run --rm -p 4000:4000 -v "$PWD":/srv/jekyll -w /srv/jekyll ruby:3.4 \
  bash -lc "bundle install && bundle exec jekyll serve --host 0.0.0.0 --baseurl /MicrosoftTutorials"
```

Then open <http://localhost:4000/MicrosoftTutorials/>.

Or open the folder in Cursor/VS Code with Dev Containers (`.devcontainer/`) and run `bundle exec jekyll serve`.
