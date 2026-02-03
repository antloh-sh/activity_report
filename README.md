# AAC Activity Reports

A lightweight, serverless reporting dashboard built with HTML5, Vanilla JavaScript, and Supabase.  
This application allows authorized users to run dynamic database reports defined in an external configuration file.

## 🏛 Architecture Overview

The application follows a decoupled design to allow for easy updates without redeploying code:

- **Frontend**: A single-page application (SPA) using Vanilla JS and CSS variables.
- **Configuration**: A `reports.json` file acting as the "Single Source of Truth" for report definitions.
- **Backend**: Supabase (PostgreSQL) handles authentication and business logic via Database Functions (RPC).

## 🚀 Features

- Dynamic report loading from a JSON file, eliminating the need to edit HTML when adding new database functions.
- Date range filtering with built-in logic to handle PostgreSQL date formatting (converts `YYYY-MM` inputs to full month ranges).
- Auto-formatting tables that detect whether the database returns a single value (big number view) or an array of objects (table view).
- Secure auth integrated with Supabase Auth for restricted access.
- Mobile responsive layout optimized for both desktop and mobile browsers.

## 📂 File Structure

```text
├── index.html          # Main application logic and UI
├── reports.json        # Configuration file defining available reports
└── README.md           # Documentation
```

## ⚙️ Configuration

### 1. Database Functions (Supabase)

The app expects Supabase RPC functions to accept `start_date` and `end_date` as arguments:

```sql
-- Example Function
CREATE OR REPLACE FUNCTION get_gui_session_count(start_date DATE, end_date DATE)
RETURNS TABLE (count BIGINT) AS $$
  SELECT count(*) FROM activity_log 
  WHERE created_at BETWEEN start_date AND end_date;
$$ LANGUAGE sql;
```

### 2. JSON Config (`reports.json`)

To add a new report, add an entry to the `reports` array.  
The `id` must match the exact name of the Supabase RPC function.

```json
{
  "reports": [
    {
      "id": "get_gui_session_count",
      "name": "GUI Session Count"
    }
  ]
}
```

## 🛠 Setup & Installation

1. Clone the repository to your local machine or web server.  
2. Configure Supabase: update the `SUPABASE_URL` and `SUPABASE_ANON_KEY` constants in `index.html` with your project credentials.  
3. Deploy:  
   - Upload `index.html` and `reports.json` to any static web host (GitHub Pages, Netlify, Vercel, or a local Apache/Nginx server).  
   - Note: Because the app fetches `reports.json` via an HTTP request, it must be served via a web server (for example, `http://localhost`) rather than opened directly as a file (`file://...`).

## 🖥 Usage

1. Log in using your Supabase credentials.  
2. Select a report from the dynamically generated dropdown.  
3. Choose a Start Month and End Month.  
4. Click **Execute Query** to view the results; they will render as a data table or a highlighted total depending on the data returned.

## 🔒 Security

- Ensure that Row Level Security (RLS) is enabled on your Supabase tables.  
- Restrict RPC execution permissions in Supabase to authenticated users only.
