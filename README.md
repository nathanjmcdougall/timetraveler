# Timetraveler

---

🚧 **This project does not exist yet!** 🚧  

This README is **aspirational**—it describes a potential tool that could be built. Any features mentioned here **do not exist yet**!

---

`timetraveler` is a Python library that brings automated version control to your data engineering pipelines—think MLFlow for your functions. With a simple decorator, `@timetraveler`, you can automatically track changes to your function’s source code, metadata, and even dependencies. This lets you run and compare multiple versions of your data pipelines side by side without the need to constantly switch branches or manually manage snapshots.

## Features

- **Automated Function Versioning:**  
  Capture and store the source code of a decorated function automatically on each call.

- **Dependency Tracking:**  
  Trace and version dependent functions decorated with `@timetraveler`, building a reproducible execution graph.

- **Snapshot Management:**  
  Easily assign and call snapshots using names or hash IDs. Use a snapshot to run an earlier version of your function.

- **Metadata Capture:**  
  Save metadata such as datetime, git commit, username, and custom comments alongside the versioned code.

- **Seamless Integration:**  
  Designed for data engineering and ML projects, letting you iterate quickly without breaking reproducibility.

## Installation
🚧 *Timetraveler does not exist yet, so you cannot install it.* 🚧  

In the future, perhaps you could install `timetraveler` via [PyPI](https://pypi.org/project/timetraveler):

```bash
# With uv
uv add timetraveler

# With pip
pip install timetraveler
```

## Usage

### Basic Decorator Usage

Decorate your function to start tracking versions:

```python
from timetraveler import timetraveler

@timetraveler
def get_data() -> pd.DataFrame:
    # Your complex data pipeline logic here
    ...
```

Each time `get_data()` is called, the current source code and metadata are recorded in the underlying database.

### Using Snapshot IDs

Assign a custom name to a snapshot and then run that version later:

```python
# Run the function and assign a snapshot name - you can only do this once!
get_data.set_name("initial_snapshot")()

# Later, run the older version using the snapshot ID
get_data.at("initial_snapshot")()
```

### Managing Dependencies

If `get_data` depends on other `@timetraveler`-decorated functions, the system will automatically record their version mappings. You can also override dependency versions manually:

```python
get_data.at("snapshot", dependents={"helper_func": "other_snapshot"})()
```

This lets you selectively roll back parts of your pipeline while keeping others current.

## How It Works

- **Automated Source Capture:**  
  On each function call, the decorator extracts the function’s source code, computes a hash, and stores the code if changes are detected.

- **Version Metadata:**  
  Information like the datetime, current git commit, environment, and user information are captured automatically, making it easy to trace the evolution of your pipeline.

- **Dependency Resolution:**  
  The decorator recursively checks for other functions decorated with `@timetraveler` to ensure that a complete, reproducible snapshot of the pipeline is maintained.

- **Dynamic Execution:**  
  When a snapshot is invoked, `timetraveler` dynamically replaces the current function code with the stored version, ensuring that you are running the exact code from that snapshot.

## **🚀 Quick Start**  

### **1️⃣ Track Function Versions**  
You can decorate any function to track its versions:  

```python
import pandas as pd
from timetraveler import timetraveler

@timetraveler
def get_data() -> pd.DataFrame:
    df = pd.DataFrame({"x": [1, 2, 3], "y": [4, 5, 6]})
    return df
```

Each time `get_data()` is executed, the function’s source code is **automatically stored in a backend database**.  
It also records:  
- The timestamp of execution  
- The git commit at the time of execution (if available)  
- The username running the script
- Other useful metadata 

### **2️⃣ Restore and Run Old Versions**  
Want to run an old version of `get_data()`?

```python
df_old = get_data.at("snapshot_id")()
```

You could set a function name snapshot (see below) or else inspect the database to get this ID.

### **3️⃣ Name Function Snapshots**  
You can set an explicit name for the snapshot assocaited with the function call:  

```python
get_data.set_name("baseline_experiment")()
```

Then, retrieve that version anytime:  

```python
df_baseline = get_data.at("baseline_experiment")()
```

This will automatically retrieve the old version of `get_data` and replace its current implementation at runtime with the older version.

### **4️⃣ Function IDs**  
By default, Timetraveler tracks functions based on **where they are defined** (i.e., their namespace).  

However, sometimes functions get **moved**, **renamed**, or **refactored into different files**. To prevent losing track of these changes, you could assign a **time-persistent ID** to a function using:  

```python
@timetraveler(name="get_data")
def get_data_v2() -> pd.DataFrame:
    df = pd.DataFrame({"x": [10, 20, 30], "y": [40, 50, 60]})
    return df
```

This ensures that **even if the function name or file location changes**, you can still access old versions:  

```python
# The ID is shared so we get the full history of `get_data_v2` including from when it was renamed from get_data
df_old = get_data_v2.at("baseline_experiment")()

# Or we can explicitly resurrect a function into the current namespace if it doesn't exist anymore 
df_old = timetraveler.get("get_data").at("baseline_experiment")()
```

This way, Timetraveler does not lose historical versions just because you refactored your code.  

## **🆚 Traditional Version Control vs. Function Version Control**  

Traditional version control systems like **Git** track **entire files**, but Timetraveler (if it existed) would track **individual functions** instead.  

| Feature  | Traditional Version Control (e.g. Git) | Function Version Control (e.g. Timetraveler) |
|----------|----------------------------------|--------------------------------|
| Tracks changes at the... | **File level** 📄 | **Function level** 🛠️ |
| Switching versions requires... | Checking out a branch | Calling `.at(snapshot_id)()` |
| Preserves dependencies? | ⚠️ Yes, within a branch | ✅ Track dependencies automatically |
| Experimentation workflow | ❌ Cannot run multiple versions concurrently | ✅Run multiple versions side-by-side|

## Extension ideas

- Call a function with added injected code (e.g. to add a missing import which has since been deleted)
- Adding comments to snapshot entries (e.g. noting a bug associated with one of them)
- [MLFlow](https://github.com/mlflow/mlflow) integration
- [pins](https://github.com/rstudio/pins-python) integration
- [Dagster](https://github.com/dagster-io/dagster) integration
- CLI for inspecting the database
- Web GUI for inspecting the database

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
