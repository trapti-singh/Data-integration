import requests

def get_apollo_leads(api_key):
    url = "https://docs.apollo.io/reference/people-api-search"
    headers = {
        "Cache-Control": "no-cache",
        "Content-Type": "application/json",
        "X-Api-Key": "https://api.apollo.io/api/v1/mixed_people/api_search" # Added quotes around the URL
    }
    data = {
        "q_organization_domains": "google.com\nmicrosoft.com",
        "person_titles": ["Marketing Manager", "VP Growth"],
        "page": 1
    }
    response = requests.post(url, headers=headers, json=data)
    return response.json()['people']
    import requests

# Since your profile is .com, we use the US endpoint
ACCOUNTS_URL = "https://accounts.zoho.com/oauth/v2/token"

# PASTE YOUR NEW GRANT CODE HERE (it expires in 10 mins!)
fresh_grant_code = "Your Token"

payload = {
    "client_id": "Your client ID",
    "client_secret": "Your Client secret",
    "grant_type": "authorization_code",
    "redirect_uri": "https://www.google.com",
    "code": fresh_grant_code
}

response = requests.post(ACCOUNTS_URL, data=payload)
data = response.json()

if "refresh_token" in data:
    print("✅ SUCCESS!")
    print(f"Your Refresh Token is: {data['refresh_token']}")
    print("Save this token! You will use it for your main Python program.")
else:
    print("❌ FAILED")
    print(data)
    import requests

# 1. Your Credentials
REFRESH_TOKEN = "YOUR_ZOHO_REFRESH_TOKEN"
CLIENT_ID = "YOUR_ZOHO_CLIENT_ID"
CLIENT_SECRET = "YOUR_ZOHO_CLIENT_SECRET"
def get_token():
    url = "https://accounts.zoho.com/oauth/v2/token"
    params = {
        "refresh_token": REFRESH_TOKEN,
        "client_id": CLIENT_ID,
        "client_secret": CLIENT_SECRET,
        "grant_type": "refresh_token"
    }
    response = requests.post(url, data=params)
    return response.json()

# Run the test
result = get_token()
if "access_token" in result:
    print("✅ Step 1 Success! Zoho is connected.")
    print(f"Temporary Access Token: {result['access_token'][:10]}...")
else:
    print("❌ Step 1 Failed. Error:", result)


import pandas as pd

# Load the file
try:
    df = pd.read_csv("/content/Leads.csv")
    print("✅ Step 1 Success! I found the file.")
    print("Here are the columns in your CSV:")
    print(list(df.columns))

    # Show the first name to confirm it's readable
    print("\nFirst lead found in file:")
    print(df.iloc[0].to_dict())
except Exception as e:
    print(f"❌ Step 1 Failed. Error: {e}")

    import pandas as pd

df = pd.read_csv("/content/Leads.csv")

# Create a list of leads in Zoho's format
zoho_leads = []

for index, row in df.head(2).iterrows(): # Testing just the first 2 rows
    # IMPORTANT: Change the names in quotes to match your Step 1 column list
    mapped_lead = {
        "First_Name": str(row.get('First Name', '')),
        "Last_Name": str(row.get('Last Name', 'Unknown')),
        "Email": str(row.get('Email', '')),
        "Company": str(row.get('Company', 'N/A'))
    }
    zoho_leads.append(mapped_lead)

print("✅ Step 2 Success! Here is what we will send to Zoho:")
print(zoho_leads)

import pandas as pd
import requests
import time

# --- 1. YOUR CREDENTIALS ---
REFRESH_TOKEN = "YOUR_ZOHO_REFRESH_TOKEN"
CLIENT_ID = "YOUR_ZOHO_CLIENT_ID"
CLIENT_SECRET = "YOUR_ZOHO_CLIENT_SECRET"

# --- 2. TOKEN FUNCTION ---
def get_access_token():
    url = "https://accounts.zoho.com/oauth/v2/token"
    params = {
        "refresh_token": REFRESH_TOKEN,
        "client_id": CLIENT_ID,
        "client_secret": CLIENT_SECRET,
        "grant_type": "refresh_token"
    }
    response = requests.post(url, data=params)
    return response.json().get("access_token")

# --- 3. BATCH UPLOAD FUNCTION ---
def push_in_batches():
    try:
        # Load your file
        df = pd.read_csv("Leads.csv")

        # Format leads
        all_leads = []
        for _, row in df.iterrows():
            all_leads.append({
               "First_Name": str(row.get('First Name', '')),
                "Last_Name": str(row.get('Last Name', 'Unknown')),
                "Email": str(row.get('Contact Email', '')),
                "Company": str(row.get('Company Name', 'N/A')),
                "Title": str(row.get('Title (explain job role)', '')),
                "Phone": str(row.get('Phone', '')),
                "Mobile": str(row.get('Mobile', '')),
                "Street": str(row.get('Address', '')),
                "City": str(row.get('City', '')),
                "State": str(row.get('State', '')),
                "Zip_Code": str(row.get('Zip Code', '')),
                "Country": str(row.get('Country', '')),
                "Description": f"FB: {row.get('Facebook Handle', '')} | LI: {row.get('LinkedIn Handle', '')}",
                "Lead_Source": "Apollo Batch Automation"
            })

        # Set up Batching
        batch_size = 100
        token = get_access_token()
        headers = {"Authorization": f"Zoho-oauthtoken {token}"}

        print(f"📦 Total leads detected: {len(all_leads)}")

        # Loop through data in chunks of 100
        for i in range(0, len(all_leads), batch_size):
            chunk = all_leads[i:i + batch_size]
            print(f"🚀 Sending batch {i//batch_size + 1}...")

            response = requests.post(
                "https://www.zohoapis.com/crm/v2/Leads",
                headers=headers,
                json={"data": chunk}
            )

            print(f"✅ Status: {response.status_code} | Batch {i//batch_size + 1} processed.")
            time.sleep(1) # Safety pause

    except Exception as e:
        print(f"❌ Error during processing: {e}")

# --- 4. THE CALL (Must be at the very bottom) ---
if __name__ == "__main__":
    print("--- STARTING SYNC ---")
    push_in_batches()
    print("--- SYNC FINISHED ---")



    
