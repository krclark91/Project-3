# Project-3
The Corner Grocer project tracks item purchases using file input and displays frequencies through a menu-driven interface. I focused on clean code, input validation, and modular design. It strengthened my skills in file I/O and data structures, while also highlighting areas for future improvement.


#include <iostream> // For input/output operations (cin, cout, cerr)
#include <fstream>  // For file stream operations (ifstream)
#include <map>      // For storing item frequencies (associative array)
#include <string>   // For string manipulation
#include <limits>   // For std::numeric_limits (used for cin.ignore)
#include <iomanip>  // For formatting output (setw, left)

// Function to load item frequencies from the file
bool LoadItemsFromFile(const std::string& filename, std::map<std::string, int>& frequencies) {
    std::ifstream inputFile(filename);

    if (!inputFile.is_open()) {
        std::cerr << "Error: Could not open file '" << filename << "'. Please ensure it exists and is accessible." << std::endl;
        std::cerr << "Hint: Make sure the file is in the same directory as your executable or provide a full path." << std::endl;
        frequencies.clear(); // Ensure map is empty if file couldn't be opened
        return false;
    }

    frequencies.clear(); // Clear existing data to ensure a fresh load
    std::string item;
    int itemsLoaded = 0;
    while (inputFile >> item) {
        // Read item by item and increment its count in the map
        frequencies[item]++;
        itemsLoaded++;
    }
    inputFile.close();

    if (itemsLoaded == 0) {
        std::cout << "Warning: No items were loaded from the file. Program functionality may be limited." << std::endl;
        return false; // Indicate that no data was loaded
    }
    else {
        std::cout << "Successfully loaded " << itemsLoaded << " items from '" << filename << "'." << std::endl;
        return true; // Indicate successful loading
    }
}

// Function to display the main menu
void DisplayMenu() {
    std::cout << "\n--- Corner Grocer - Main Menu ---" << std::endl;
    std::cout << "1. Look up frequency of a specific item" << std::endl;
    std::cout << "2. Print frequency of all items" << std::endl;
    std::cout << "3. Print item frequency histogram" << std::endl;
    std::cout << "4. Exit program" << std::endl;
    std::cout << "---------------------------------" << std::endl;
}

// Function to get and validate user choice
int GetChoice() {
    int choice;
    std::cout << "Enter your choice: ";
    // Loop until valid input is received
    while (!(std::cin >> choice) || choice < 1 || choice > 4) {
        std::cout << "Invalid input. Please enter a number between 1 and 4: ";
        std::cin.clear(); // Clear error flags
        // Discard invalid input from the buffer
        std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
    }
    // Clear the rest of the line buffer just in case (e.g., if user typed "1 extra_text")
    std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
    return choice;
}

// Menu Option One: Look up frequency of a specific item
void FindItemFrequency(const std::map<std::string, int>& frequencies) {
    std::cout << "\n--- Look Up Item Frequency ---" << std::endl;
    if (frequencies.empty()) {
        std::cout << "No item data available. Please load items from the file first." << std::endl;
        return;
    }

    std::string searchItem;
    std::cout << "Enter the item name to look for: ";
    std::cin >> searchItem; // Reads a single word

    // FIX: Consume the remaining newline character in the input buffer.
    // This ensures std::cin.get() in main will wait for a new Enter press.
    std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');

    // Find the item in the map
    auto it = frequencies.find(searchItem);
    if (it != frequencies.end()) {
        std::cout << searchItem << " appeared " << it->second << " time(s)." << std::endl;
    }
    else {
        std::cout << searchItem << " was not found in the purchase list." << std::endl;
    }
}

// Menu Option Two: Print frequency of all items
void PrintAllFrequencies(const std::map<std::string, int>& frequencies) {
    std::cout << "\n--- All Item Frequencies ---" << std::endl;
    if (frequencies.empty()) {
        std::cout << "No item data available to print frequencies. Please load items from the file first." << std::endl;
        return;
    }

    // Iterate through the map and print each item and its frequency
    for (const auto& pair : frequencies) {
        std::cout << std::left << std::setw(15) << pair.first << " " << pair.second << std::endl;
    }
}

// Menu Option Three: Print item frequency histogram
void PrintHistogram(const std::map<std::string, int>& frequencies) {
    std::cout << "\n--- Item Frequency Histogram ---" << std::endl;
    if (frequencies.empty()) {
        std::cout << "No item data available to create a histogram. Please load items from the file first." << std::endl;
        return;
    }

    // Iterate through the map and print each item followed by asterisks
    for (const auto& pair : frequencies) {
        std::cout << std::left << std::setw(15) << pair.first << " ";
        for (int i = 0; i < pair.second; ++i) {
            std::cout << "*"; // Print an asterisk for each frequency count
        }
        std::cout << std::endl;
    }
}

// FIX: Corrected main function signature from ')' to '{'
int main() {
    // FIX: Declare the map to store item frequencies
    std::map<std::string, int> itemFrequencies;

    // FIX: Declare the filename string and correctly escape backslashes
    // Or, for better cross-platform compatibility, use forward slashes:
    // std::string filename = "U:/Corner/Debug/CS210_Project_Three_Input_File.txt";
    // Using double backslashes for direct fix of original literal style:
    std::string filename = "U:\\Corner\\Debug\\CS210_Project_Three_Input_File.txt";

    // FIX: Removed unused and redundant file stream declarations
    // ifstream inputFile("U:\\Corner\\Debug\\CS210_Project_Three_Input_File.txt");
    // ofstream outputFile("CS210_Project_Three_Input_File.txt"); // This was likely a typo as it points to the input file.

    // Attempt to load items from the file at the start of the program
    // It's good practice to check the return value, though the current functions
    // handle empty maps gracefully.
    LoadItemsFromFile(filename, itemFrequencies);

    int choice;
    do {
        DisplayMenu();
        choice = GetChoice();

        switch (choice) {
        case 1:
            FindItemFrequency(itemFrequencies);
            break;
        case 2:
            PrintAllFrequencies(itemFrequencies);
            break;
        case 3:
            PrintHistogram(itemFrequencies);
            break;
        case 4:
            std::cout << "Exiting program. Goodbye!" << std::endl;
            break;
        default:
            // This case should not be reached due to GetChoice validation
            std::cout << "Invalid choice. This should not happen." << std::endl;
            break;
        }

        // Pause before showing the menu again, unless exiting
        if (choice != 4) {
            std::cout << "\nPress Enter to continue...";
            // std::cin.get() will now correctly wait for user input because
            // GetChoice() and FindItemFrequency() now clear their own newlines.
            std::cin.get();
        }
    } while (choice != 4); // Continue loop until the user chooses to exit

    return 0;
}
