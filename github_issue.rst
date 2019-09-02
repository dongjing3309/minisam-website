
Submit a Github issue
===========================================

Please use Github issue tracker for general questions and reporting bugs.
Before submitting a Github issue please follow below guideline.

Before submitting
---------------------------

- First of all, check to `FAQ page <faq.html>`_, see whether it's a common question.
- Have a quick search in `existing Github issues <https://github.com/dongjing3309/minisam/issues>`_, other people may have the same question before.
- If you have a runtime problem (runtime error or segfault), compile miniSAM in **Debug** mode and test again. In miniSAM there are lots of checks are only compiled and performed in Debug mode for performance reasons, you will get more detailed error messages by testing your code in Debug mode.

Submitting an issue
---------------------------

- Basic information of your system and library.
   - OS and compiler information.
   - Eigen version.
   - Sophus and solver libs version (if use any).
- If you have a runtime problem, please provide minimal example (Python code snippet or C++ executable) to reproduce the problem. Do not provide very large code snippet, or code needs any external data.
- Select proper labels in Github issue.