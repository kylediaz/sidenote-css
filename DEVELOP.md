# DEVELOP.md

Run this command to regenerate `index.html`.

```bash
pandoc CONTENT.md \
  --standalone \
  --template=article.html \
  --syntax-highlighting=none \
  -o index.html
```
