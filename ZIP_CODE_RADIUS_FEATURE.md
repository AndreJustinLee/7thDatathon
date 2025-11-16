# Zip Code Radius Search Feature - Complete Implementation

## Overview
Implemented a comprehensive zip code-based radius search feature that allows students to find colleges within a specific distance from their home location.

## What Was Implemented

### 1. Core Distance Utilities (`src/distance_utils.py`)

**New Module Created** with the following functions:

#### `haversine_distance(lat1, lon1, lat2, lon2) -> float`
- Calculates great circle distance between two points on Earth
- Uses Haversine formula
- Returns distance in miles
- Accurate for any two points globally

#### `get_zip_coordinates(zip_code: str) -> Optional[Tuple[float, float]]`
- Converts 5-digit US zip code to latitude/longitude coordinates
- Supports both `uszipcode` and `pgeocode` libraries with automatic fallback
- Returns `(latitude, longitude)` tuple or `None` if not found

#### `filter_by_radius(df, zip_code, radius_miles) -> pd.DataFrame`
- Filters colleges within specified radius of a zip code
- Automatically calculates distances using Haversine formula
- Adds `distance_miles` column to results
- Sorts results by distance (closest first)
- Returns filtered and sorted DataFrame

#### `add_distance_column(df, zip_code) -> pd.DataFrame`
- Adds distance information without filtering
- Useful for showing distances even when not applying radius constraint
- Returns DataFrame with `distance_miles` column

### 2. User Profile Updates (`src/user_profile.py`)

**Added New Fields:**
- `zip_code: Optional[str]` - Student's 5-digit zip code
- `radius_miles: Optional[int]` - Maximum distance willing to travel

**Added Validation:**
- Zip code must be exactly 5 digits
- Auto-normalizes zip codes (strips non-digit characters)
- Radius requires zip code to be set
- Radius must be positive if specified

**Updated String Representation:**
- Shows zip code and radius in profile display
- Clean formatting with location information grouped

### 3. Scoring Integration (`src/scoring.py`)

**Updated `filter_colleges_for_user()` function:**

```python
# Filter by zip code radius (if specified)
if profile.zip_code and profile.radius_miles:
    print(f"  Applying radius filter: {profile.radius_miles} miles from zip {profile.zip_code}")
    filtered = filter_by_radius(filtered, profile.zip_code, profile.radius_miles)
    print(f"  Radius filter: {len(filtered)} institutions within {profile.radius_miles} miles")
elif profile.zip_code:
    # Add distance column even if not filtering
    filtered = add_distance_column(filtered, profile.zip_code)
```

**Features:**
- Automatic radius filtering when zip and radius provided
- Distance calculation even without radius (for display)
- Informative console output showing filtering results
- Integrates seamlessly with existing filters (budget, state, size, etc.)

### 4. Chat Interface Updates (`src/app_streamlit_chat.py`)

**Added Two New Questions:**
1. Question 11: "Do you want to search for colleges near you? Enter your 5-digit zip code, or type 'skip' to search all locations:"
2. Question 12: "How many miles away are you willing to travel for college? (e.g., 50, 100, 200, or 'any' for no limit)"

**Input Processing:**
- Zip code: Extracts 5 digits, validates format, allows 'skip'
- Radius: Parses integer miles, allows 'any' or 'skip', auto-skips if no zip

**Manual Form Updates:**
- Added "Location Preferences" section
- Zip code text input with placeholder "90210"
- Radius number input with min=0, help text
- Smart validation (radius only applies if zip provided)

**Profile Creation:**
- Both chat and manual form create profiles with zip_code and radius_miles
- Properly handles None values for skipped questions

### 5. Dependencies (`requirements.txt`)

**Added:**
```
pgeocode>=0.5.0
```

**Why pgeocode:**
- Lightweight alternative to uszipcode
- No SSL certificate issues
- Works offline after initial dataset download
- Supports all US zip codes
- Free and open source

## User Experience

### Chat Flow Example

```
Assistant: Do you want to search for colleges near you?
           Enter your 5-digit zip code, or type 'skip' to search all locations:
User: 90210