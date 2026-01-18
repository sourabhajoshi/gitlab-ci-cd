## **Continuous Integration (CI) with GitLab**

Continuous Integration (CI) is a practice where, Every time you push code to GitLab, your code is automatically checked, tested, and verified.

In simple words:
- You write code
- You push (upload) it to GitLab
- GitLab automatically runs checks to see if your code:
    - Builds correctly
    - Has no errors
    - Passes tests
- This happens every time, not manually.

Without CI:
- Developers manually run tests
- Errors are found late
- Code breaks often

With CI:
- Errors are found early
- Code quality improves
- Team members don’t break each other’s code


### **Build a Project**

We build a project to convert developer-friendly code into optimized, browser-ready, production-ready files that real users can use safely and fast. (Developer ಬರೆಯುವ code ಅನ್ನು real users (browser / server) ಬಳಸಲು ready ಆಗುವಂತೆ convert ಮಾಡುವುದು)

**Why do we need to build a project**

1. Browsers cannot understand modern code

Browser-friendly code ಮಾಡೋದು build ಕೆಲಸ
```
const add = (a, b) => a + b;
```
Some browsers: Don’t understand const, arrow functions

After build:
```
function add(a, b) {
  return a + b;
}
```
Works in all browsers

2. Performance (Speed) improvement

Website fast ಆಗಿ open ಆಗೋಕೆ build ಬೇಕು

Before build:
- Many files
- Large size
- Slow loading

After build:
- Fewer files
- Smaller size
- Fast loading

3. Removes developer-only code

Userಗೆ ಬೇಡವಾದ code remove ಆಗುತ್ತೆ

Development code contains:
- console.log
- Debug warnings
- Hot reload code

Build:
- Removes dev code
- Keeps production code

4. Required for deployment

Serverಗೆ build output ಮಾತ್ರ ಬೇಕು.

Servers understand:
- HTML
- CSS
- JavaScript

Servers do NOT understand:
- React JSX
- ES6+
- TypeScript

**What happens during the Build process?**

**Compilation**
Modern code → Old compatible code
- Converts modern JavaScript (ES6+) into browser-supported JavaScript
- Ensures compatibility with all browsers

New JS → Old JS convert ಆಗುತ್ತೆ

**Bundling**

Multiple files → Few files
- Combines many JavaScript, CSS files into fewer files
- Reduces number of network requests

Files combine ಆಗುತ್ತವೆ

**Minification**

Readable code → Smaller code
- Removes spaces, comments, and long variable names
- Reduces file size

File size ಕಡಿಮೆ ಆಗುತ್ತೆ

**Optimization**

Performance improvements
- Image compression
- CSS optimization
- Remove unused code (tree shaking)

Performance improve ಆಗುತ್ತೆ

### **Framework-wise Build Commands**

```
<!-- React -->
npm install
npm run build

Output folder: build/

<!-- Angular -->
npm install
npm run build
ng build

Output folder: dist/

<!-- vue -->
npm install
npm run build

Output folder: dist/
```


