import requests
import json
import pandas as pd
import os

url = "https://ssapi.shipstation.com//shipments/?storeId=959579&pageSize=500&createDateStart=1/1/2023&page={page}"
headers = {
    'Authorization': 'Basic (My KEY)
}

#Iterate through JSON response and combine all data

combined_response = []
for page in range(1, 5):
    page_url = url.format(page=page)
    response = requests.get(page_url, headers=headers)
    response_data = json.loads(response.text)
    combined_response.extend(response_data['shipments'])

 
combined_json_response = json.dumps(combined_response)

df = pd.DataFrame(combined_response)
 
# Filter data to remove internal / unrelated shipping costs 

filtered_df = df[~df['shipTo'].apply(lambda x: 'PWCC Marketplace' in x['name'] or x['name'].lower().startswith('attn') or 'booth' in x['name'].lower())]
filtered_df['shipTo'] = filtered_df['shipTo'].apply(lambda x: x['name'])
filtered_df = filtered_df[['shipTo', 'orderId', 'shipmentCost']]

total_difference = filtered_df['shipmentCost'].sum()
rounded_total_difference = round(total_difference)

print("Rounded Total Difference:", rounded_total_difference)
 
# Create a new row with the sum of total shipment cost

sum_row = pd.DataFrame({'shipTo': ['Total'], 'orderId': [''], 'shipmentCost': [rounded_total_difference]})
filtered_df = pd.concat([filtered_df, sum_row], ignore_index=True)

# Export DataFrame to CSV file in the Downloads folder

output_folder = os.path.expanduser("~/Downloads")
output_file = os.path.join(output_folder, "output.csv")

filtered_df.to_csv(output_file, index=False)

print(f"Data exported to {output_file} successfully.")
