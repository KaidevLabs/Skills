# Scratch files

- Keep throwaway scripts, dumps, fixtures, and benchmark output in a clearly named temporary directory at the repository root (for example, .tmp/) when that matches the project's conventions. Avoid system temporary locations when tools or containers need to access the files.
- Temporary files must not become part of the product or test suite by accident. Keep the directory ignored by Git, and track only an intentional ignore rule if needed.
- Delete scratch files when the task is done. Store nothing long-lived there; promote useful scripts or fixtures into a documented, maintained location.
