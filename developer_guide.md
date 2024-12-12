# Developer Guide for XRPrism

This guide provides instructions for setting up the development environment, code standards, best practices, and project structure for developers contributing to XRPrism.

---

## **1. Development Environment Setup**

### **Required Tools & Versions**
To contribute to XRPrism, ensure the following tools are installed:

- **Python:** Latest version
- **Docker:** Latest version (Docker Desktop for ease of use)
- **Docker Compose:** Latest version
- **Invoke:** For task automation (`pip install invoke`)
- **CUDA:** Latest version (for GPU acceleration)
- **NVIDIA Studio Driver:** Latest version (required for CUDA compatibility)

### **Minimum Machine Specs**
- **CPU:** AMD Series 5 or Intel Equivalent
- **Memory:** 8GB or more
- **GPU:** NVIDIA 20 Series or higher

### **Machine Specs Used for Testing**
- **CPU:** AMD Ryzen 7 2700x
- **Memory:** 32GB RAM (3200MHz)
- **GPU:** NVIDIA RTX 3070 (8GB VRAM)

### **Installation Steps**
1. Clone the repository to your local machine:
   ```bash
   git clone https://github.com/your-repo/XRPrism.git
   cd XRPrism
   ```

2. Ensure all necessary packages are installed using the provided `requirements.txt` file:
   ```bash
   pip install -r requirements.txt
   ```

3. Set up CUDA and NVIDIA Studio Driver on your system:
   - Download and install [CUDA](https://developer.nvidia.com/cuda-toolkit).
   - Install the latest [NVIDIA Studio Driver](https://www.nvidia.com/studio).

4. Confirm the installation of all required dependencies by running:
   ```bash
   invoke test_all
   ```

### **System Compatibility**
- This project was tested on **WSL2 on Windows 11 Pro** but should run on any Linux distro commonly used for AI/ML development.

---

## **2. Code Standards & Best Practices**

### **1. Code Formatting Tools**
To maintain a clean and consistent codebase, the following tools are required:

- **Black:** For automatic code formatting.
- **Flake8:** For linting and enforcing PEP 8 compliance.
- **isort:** For organizing imports.

Run the following command to format, lint, and sort imports:
```bash
invoke format_code
invoke lint
```
### **2. Standards to Enforce**
- **PEP 8 Compliance:** Follow the standard [PEP 8](https://peps.python.org/pep-0008/) guidelines.
- **Docstrings:** Use Google-style docstrings for functions and classes.
- **Type Hints:** Use type hints in all function signatures.

#### **Example Function with Docstring & Type Hints**
```python
def fetch_data(account: str) -> dict:
    """
    Fetch transaction data for a given account from XRPScan API.

    Args:
        account (str): The XRP account address.

    Returns:
        dict: Transaction data from the API.
    """
    # Fetching logic here
```

### **3. Code Review Process**
- **Branch Naming Convention:**
  - `feature/branch-name`: For new features.
  - `bugfix/branch-name`: For fixing bugs.
  - `hotfix/branch-name`: For urgent production fixes.

- **Commit Message Guidelines:** Use the following conventional commit format:
  - `feat: add feature description`
  - `fix: describe the issue fixed`
  - `docs: update documentation`
  - `refactor: refactor code without changing functionality`

#### **Example Commit Message**
```bash
git commit -m "feat: add support for data ingestion from new API"
```

### **4. Testing & Automation**
- **Testing Framework:** Use `pytest` for all unit and integration tests.
- **Automation Commands:** Use the following Invoke tasks:
  - Run all tests:
    ```bash
    invoke test_all
    ```
  - Run specific tests:
    ```bash
    pytest tests/test_file.py
    ```

---

## **3. How to Add Features or Fix Bugs**

This section explains how to contribute new features or fix bugs in XRPrism.

---

### **1. Creating a New Feature**

1. **Create a Feature Branch:**
   - Use a descriptive branch name based on the feature you're working on:
     ```bash
     git checkout -b feature/feature-name
     ```

2. **Implement the Feature:**
   - Follow the project's code standards and best practices.
   - Keep your changes focused and modular.
   - Add meaningful comments where necessary.

3. **Add Tests:**
   - Write unit and integration tests for the new feature.
   - Ensure all tests pass by running:
     ```bash
     invoke test_all
     ```

4. **Commit Changes:**
   - Use meaningful commit messages:
     ```bash
     git add .
     git commit -m "feat: add support for feature-name"
     ```

5. **Push & Create a Pull Request:**
   - Push the branch to the repository:
     ```bash
     git push origin feature/feature-name
     ```
   - Open a pull request (PR) and request a review.

---

### **2. Fixing Bugs**

1. **Reproduce the Bug:**
   - Verify the bug exists and isolate the cause.
   - Create a minimal test case that replicates the bug.

2. **Create a Bugfix Branch:**
   ```bash
   git checkout -b bugfix/bug-name
   ```

3. **Apply the Fix:**
   - Make the required changes to fix the bug.
   - Ensure all tests pass by running:
     ```bash
     invoke test_all
     ```

4. **Write Regression Tests:**
   - Add tests that ensure the bug won’t reoccur.

5. **Commit & Push the Fix:**
   ```bash
   git add .
   git commit -m "fix: resolve issue with bug-name"
   git push origin bugfix/bug-name
   ```

6. **Create a Pull Request:**
   - Open a PR and provide a clear description of the fix.

---

### **3. Workflow Summary**

- **Branch Naming Convention:**
  - `feature/branch-name`: For new features.
  - `bugfix/branch-name`: For fixing bugs.

- **PR Creation:**
  - Clearly describe changes made.
  - Add references to relevant issues (if applicable).

- **Code Review Process:**
  - Ensure tests pass on CI (if enabled).
  - Respond to review comments promptly.
  - Apply suggested changes as needed.

---

### **4. Best Practices**

- **Keep Changes Small:** Submit smaller, focused PRs for easier reviews.
- **Meaningful Commit Messages:** Use clear and descriptive commit messages.
- **Update Documentation:** Ensure relevant documentation files are updated when introducing new features or changing existing behavior.

---

## **4. Folder Structure Breakdown**

This section provides an overview of the XRPrism repository structure, describing the purpose of key folders and files.

---

### **1. Repository Overview**

The XRPrism repository is organized into multiple directories, each serving a specific purpose. This structure ensures modularity, scalability, and ease of maintenance.

---

### **2. Key Folders & Files**

| **Directory/File**     | **Description**                                      |
|------------------------|------------------------------------------------------|
| `src/`                 | Core application code (models, inference, training). |
| `docs/`                | Documentation files (developer guides, API docs).    |
| `tests/`               | Unit and integration tests.                          |
| `models/`              | Storage for trained models.                          |
| `data/`                | Contains raw and processed data files.               |
| `deployment/`          | Dockerfiles, CI/CD scripts, and deployment configs.  | 

---

### **3. Detailed Breakdown**

#### **1. `src/` - Core Application Code**
- **`core/`**: Core business logic (e.g., model training, evaluation).
- **`data/`**: Data processing modules.
- **`inference/`**: AI inference modules.
- **`tasks.py`**: Automation tasks using Invoke.
  
#### **2. `docs/` - Documentation Files**
- **`developer_guide.md`**: Development setup, coding standards, and contribution guidelines.
- **`api_documentation.md`**: API reference (if applicable).

#### **3. `tests/` - Unit & Integration Tests**
- **`test_models.py`**: Model-related tests.
- **`test_api.py`**: API endpoint tests (if applicable).

#### **4. `models/` - Trained Model Storage**
- **`prism_model_vX.Y.Z.pth`**: Model checkpoints by version.
- **`scaler.joblib`**: Model scaler file.

#### **5. `data/` - Raw & Processed Data**
- **`raw/`**: Raw data files fetched from APIs.
- **`processed/`**: Processed CSV files used for AI training.

#### **6. `deployment/` - Deployment Configs**
- **`docker-compose.yml`**: Docker service definitions.
- **`prism_collector.Dockerfile`**: Dockerfile for the data collector service.
- **`format.Dockerfile`**: Dockerfile for data formatting service.

---

### **4. Key Files Overview**

| **File**               | **Description**                                |
|------------------------|------------------------------------------------|
| `tasks.py`             | Task management using Invoke.                  |
| `docker-compose.yml`   | Docker service configuration.                  |
| `requirements.txt`     | Required Python packages and dependencies.     |
| `README.md`            | Project overview and getting started guide.    |

---



