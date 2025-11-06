# Parking Management System in C

A comprehensive command-line parking management system developed in C, featuring dynamic memory allocation, custom data structures, and complex billing calculations. Built for the Introduction to Algorithms and Data Structures course at Instituto Superior Técnico.

## 📋 Overview

This project implements a complete parking management system capable of handling up to 20 parking lots, tracking vehicle entries and exits, calculating fees based on customizable billing schemes, and generating detailed reports. The system demonstrates proficiency in C programming, data structure design, memory management, and algorithm implementation.

## 🎯 Key Features

### Core Functionality
- **Multi-Parking Management**: Handle up to 20 parking facilities simultaneously
- **Vehicle Tracking**: Register and monitor vehicle entries/exits with chronological validation
- **Dynamic Billing System**: Configurable pricing models with three-tier rate structure
- **Advanced Reporting**: Generate billing summaries and vehicle history reports
- **Date/Time Validation**: Complete temporal consistency checking
- **Special Date Handling**: Automatic February 29th closure (superstition feature!)

### Billing System
The system implements a sophisticated three-tier pricing model:
- **X**: Cost per 15 minutes in the first hour
- **Y**: Cost per 15 minutes after the first hour
- **Z**: Maximum daily charge (24-hour cap)

#### Billing Examples
For a parking lot with rates: X=€0.25, Y=€0.30, Z=€15.00

**Example 1**: 2-hour stay
- Calculation: 4 × €0.25 (first hour) + 4 × €0.30 (second hour) = €2.20

**Example 2**: 3 days + 2 hours (Entry: 01-04-2024 08:00, Exit: 04-04-2024 10:00)
- Calculation: 3 × €15.00 + 4 × €0.25 + 4 × €0.30 = €47.20

**Example 3**: 3 days + 15 hours (Entry: 01-04-2024 08:00, Exit: 04-04-2024 23:00)
- Calculation: 3 × €15.00 + €15.00 = €60.00 (capped at daily maximum)

## 🏗️ System Architecture

### Data Structures

```c
// Parking lot with capacity and billing configuration
typedef struct {
    char name[MAX_NAME];
    int capacity;
    int available;
    float rate_15min_first_hour;
    float rate_15min_after_hour;
    float max_daily_rate;
} Parking;

// Vehicle with license plate validation
typedef struct {
    char plate[9];  // Format: XX-YY-ZZ
    int currently_parked;
    ParkingEntry *entries;
} Vehicle;

// Entry/Exit record with timestamp
typedef struct {
    char parking_name[MAX_NAME];
    DateTime entry_time;
    DateTime exit_time;
    float amount_paid;
} ParkingEntry;
```

### License Plate Format
- **Format**: `XX-YY-ZZ` (3 pairs separated by hyphens)
- **Rules**: 
  - Each pair contains either 2 letters (A-Z) OR 2 digits (0-9)
  - Cannot mix letters and digits in a single pair
  - Must have at least one letter pair AND one digit pair
- **Valid Examples**: `AA-00-AA`, `12-AB-34`, `AB-12-CD`
- **Invalid Examples**: `A1-23-BC` (mixed pair), `AA-BB-CC` (no digits)

## 🎮 Command Interface

### Available Commands

| Command | Description | Usage |
|---------|-------------|-------|
| `p` | Create parking lot or list all | `p [name capacity X Y Z]` |
| `e` | Register vehicle entry | `e <parking> <plate> <date> <time>` |
| `s` | Register vehicle exit | `s <parking> <plate> <date> <time>` |
| `v` | List vehicle history | `v <plate>` |
| `f` | Show parking billing | `f <parking> [date]` |
| `r` | Remove parking lot | `r <parking>` |
| `q` | Exit program | `q` |

### Command Examples

#### Create Parking Lots
```bash
p Saldanha 200 0.20 0.30 12.00
p "CC Colombo" 400 0.25 0.40 20.00
```

#### Register Entry/Exit
```bash
e Saldanha AA-00-AA 01-03-2024 08:34
Saldanha 199

s Saldanha AA-00-AA 01-03-2024 10:59
AA-00-AA 01-03-2024 08:34 01-03-2024 10:59 1.60
```

#### Vehicle History
```bash
v AA-00-AA
Saldanha 01-03-2024 08:34 01-03-2024 10:59
"CC Colombo" 02-03-2024 14:20 02-03-2024 16:45
```

#### Billing Reports
```bash
# Daily summary for all dates
f Saldanha
01-03-2024 125.40
02-03-2024 340.80

# Detailed report for specific date
f Saldanha 01-03-2024
AA-00-AA 10:59 1.60
BB-11-BB 18:30 12.00
```

## 🔒 Validation & Error Handling

### Chronological Validation
- All entries/exits must be chronologically ordered
- Cannot register past events after future events
- Vehicles cannot enter twice without exiting

### Error Messages

| Error | Condition |
|-------|-----------|
| `parking already exists` | Duplicate parking name |
| `invalid capacity` | Capacity ≤ 0 |
| `invalid cost` | Rates ≤ 0 or not increasing (X < Y < Z) |
| `too many parks` | Maximum 20 parks reached |
| `no such parking` | Parking lot doesn't exist |
| `parking is full` | No available spaces |
| `invalid licence plate` | Incorrect plate format |
| `invalid vehicle entry` | Vehicle already parked |
| `invalid vehicle exit` | Vehicle not in specified parking |
| `invalid date` | Date/time format error or temporal inconsistency |

### Special Rules
- **February 29th**: Parking lots closed (no charges for this date)
- **Names with spaces**: Must be enclosed in quotes (e.g., `"CC Colombo"`)
- **Input limit**: Commands cannot exceed `BUFSIZ` bytes (8192 bytes)

## 🛠️ Technical Specifications

### Compilation
```bash
gcc -O3 -Wall -Wextra -Werror -Wno-unused-result -o proj1 *.c
```

### Execution
```bash
./proj1 < input.txt > output.txt
```

### Testing
```bash
# Run automated tests
make -C public-tests/

# Manual diff comparison
diff expected.out actual.out
```

### Allowed Libraries
- `stdio.h` - Input/output operations
- `stdlib.h` - Memory allocation, conversions
- `ctype.h` - Character validation
- `string.h` - String manipulation

### Restrictions
- ❌ No `goto` statements
- ❌ No `extern` declarations
- ❌ No `qsort()` function (must implement custom sorting)
- ✅ Must use dynamic memory allocation for variable-size structures

### Static Allocation Limits (if used)
- Parking name length: 50 bytes
- Maximum vehicles in system: 10,000
- Maximum entries per vehicle: 10,000

## 📊 Data Management

### Memory Management
The system uses **dynamic memory allocation** for:
- Variable number of parking lots (up to 20)
- Variable number of vehicles (unlimited)
- Variable entry/exit history per vehicle

### Date/Time Handling
- **Date Format**: `DD-MM-YYYY`
- **Time Format**: `HH:MM`
- **Billing Granularity**: 15-minute intervals
- **Special Handling**: Leap year February 29th exclusion

### Monetary Values
- Stored as `float` (IEEE 754)
- Displayed with 2 decimal places (`%.2f`)
- Calculated in 15-minute increments

## 📚 Implementation Highlights

### Algorithm Complexity
- **Vehicle lookup**: O(n) linear search (optimizable with hash table)
- **Entry sorting**: O(n log n) for chronological ordering
- **Billing calculation**: O(1) for single entry, O(n) for reports
- **Parking availability**: O(1) counter-based tracking

### Design Patterns
- **Command Pattern**: Each command is a discrete operation
- **Data Validation**: Input sanitization at entry points
- **Separation of Concerns**: Distinct modules for I/O, business logic, data management

## Development Process

### Incremental Development Approach
1. ✅ Implement parking creation (`p` command)
2. ✅ Add vehicle entry registration (`e` command)
3. ✅ Implement exit with billing (`s` command)
4. ✅ Add vehicle history (`v` command)
5. ✅ Implement billing reports (`f` command)
6. ✅ Add parking removal (`r` command)

### Testing Strategy
- Test each command individually before integration
- Verify input/output formatting precisely
- Check edge cases (full parking, invalid dates, etc.)
- Validate chronological ordering
- Test memory allocation/deallocation

## 🔗 Resources

- [GitLab Repository](https://gitlab.rnl.tecnico.ulisboa.pt/) - RNL IST
- [Course Guidelines](https://fenix.tecnico.ulisboa.pt/disciplinas/IAED23/2023-2024/2-semestre/guidelines)
- [Debugging Tips](https://fenix.tecnico.ulisboa.pt/disciplinas/IAED23/2023-2024/2-semestre/debugging)

## 📝 License

This project was developed as coursework for academic purposes at Instituto Superior Técnico.

## 👥 Author

Developed as an individual project for the Introduction to Algorithms and Data Structures course.

---

*Note: This implementation showcases fundamental programming skills in C, including dynamic memory management, data structure design, algorithm implementation, and comprehensive input validation.*
