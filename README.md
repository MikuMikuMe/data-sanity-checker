# data-sanity-checker

Creating an automated data sanity checker involves crafting a Python program that validates and cleanses datasets according to predefined quality standards. This program will typically read data from a file (e.g., CSV), perform checks, and make corrections or report errors. Below is an example of such a program. This implementation will assume the dataset is a CSV file, but you can adapt this for other formats as needed.

```python
import pandas as pd

class DataSanityChecker:
    def __init__(self, filepath):
        """Initialize the DataSanityChecker with a file path to the dataset."""
        self.filepath = filepath
        self.data = None

    def load_data(self):
        """Load the data from a CSV file."""
        try:
            self.data = pd.read_csv(self.filepath)
            print("Data loaded successfully.")
        except FileNotFoundError:
            raise Exception("The file was not found. Please check the filepath.")
        except pd.errors.EmptyDataError:
            raise Exception("The file is empty. Please provide a valid dataset.")
        except pd.errors.ParserError:
            raise Exception("Error parsing data. Ensure the file is a valid CSV format.")

    def check_missing_values(self):
        """Check for missing values in the dataset."""
        if self.data.isnull().values.any():
            print("Missing values found in the dataset.")
            self.data.fillna(self.data.mean(numeric_only=True), inplace=True)  # Replace missing numeric values with mean
            self.data.fillna("Missing", inplace=True)  # Replace non-numeric missing values
            print("Missing values have been handled.")
        else:
            print("No missing values found.")

    def check_data_types(self):
        """Check and enforce correct data types."""
        try:
            # Add specific checks and conversions according to your dataset schema
            for column in self.data.columns:
                if 'date' in column.lower():
                    self.data[column] = pd.to_datetime(self.data[column], errors='coerce')
            print("Data types have been checked and coerced where necessary.")
        except Exception as e:
            print(f"An error occurred while checking data types: {e}")

    def check_for_duplicates(self):
        """Check for and remove duplicate rows."""
        duplicate_count = self.data.duplicated().sum()
        if duplicate_count > 0:
            print(f"Found {duplicate_count} duplicate rows. Removing duplicates.")
            self.data.drop_duplicates(inplace=True)
        else:
            print("No duplicate rows found.")

    def validate_data(self):
        """Run all validation checks."""
        print("Starting data validation process...")
        self.check_missing_values()
        self.check_data_types()
        self.check_for_duplicates()
        print("Data validation completed.")

    def save_clean_data(self, output_filepath):
        """Save the clean data to a new CSV file."""
        try:
            self.data.to_csv(output_filepath, index=False)
            print(f"Cleaned data saved successfully to {output_filepath}")
        except Exception as e:
            print(f"An error occurred while saving the file: {e}")

if __name__ == "__main__":
    # Example usage:
    filepath = 'your_dataset.csv'
    output_filepath = 'cleaned_dataset.csv'
    
    checker = DataSanityChecker(filepath)
    try:
        checker.load_data()
        checker.validate_data()
        checker.save_clean_data(output_filepath)
    except Exception as e:
        print(f"An error occurred: {e}")
```

### Key Components:

- **Loading Data:** It includes error handling for common file loading issues.
- **Missing Value Handling:** Detects missing values and fills them with mean for numeric and "Missing" for others.
- **Data Type Coercion:** Changes columns with 'date' in their name to datetime, with error handling.
- **Duplicate Removal:** Identifies and removes duplicate rows.
- **Validation Process:** Runs all checks sequentially and reports progress.
- **Output:** Saves cleansed data to a new file, with error handling for file operations.

This script can be expanded with more specific validation rules based on your dataset requirements, such as range checks, pattern matching (for strings), or data integrity constraints.