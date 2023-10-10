# Make Your First API Call

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem';

Now that you have logged in, created your first client and obtained your API access.

```mdx-code-block
<Tabs>
<TabItem value="py" label="Python">
```

Lets write our first application, `Hello World` which makes a simple PING call.

```python
import requests
import json
 
# Global variables with values to be replaced for each customer
auth_token_get_url = "<Replace with the Token URL from Developer portal>"
api_url = "<Replace with API Base URL from Developer portal>/v1/ping"
api_gateway_key = "<Replace Api Key from Developer portal>"
client_id = "<Replace Client Id obtained from Developer portal>"
client_secret = "<Replace Client Secret from Developer portal>"
# =======================================================================================
#                               Authentication Process
# ======================================================================================
# Set the required header values for getting Authentication token
headers = {
    'authorization': 'bearer ' + api_gateway_key,
    'cache-control': 'no-cache',
    'content-type': 'application/x-www-form-urlencoded',
}
# Set the required body values for getting Authentication token
data = {
    'grant_type': 'client_credentials',
    'client_id': client_id,
    'client_secret': client_secret
}
# Raise the request for getting the Authentication token
response = requests.post(auth_token_get_url, headers=headers, data=data)
# Convert the response string to Json object so that token value can be extracted
# Token expiration duration is available as part of token and is represented in seconds
responseJson = json.loads(response.content)
# =======================================================================================
#                               API Access Process
# ======================================================================================
# Set the Authorization header with the token obtained in the previous step and api key
headers = {'Authorization': 'bearer ' +
           responseJson["access_token"], 'x-api-key': api_gateway_key}
# Raise the request with the header
response = requests.get(api_url, headers=headers)
# print the response
print(response.text)
```

```mdx-code-block
</TabItem>
<TabItem value="c" label="C#">
```

```c
using System;
using System.Collections.Generic;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text.Json;
using System.Threading.Tasks;
 
namespace PingClient
{
    className Program
    {
 
//Global variables
        public static string auth_token_get_url = "<Replace with the Token URL from Developer portal>";
        public static string api_url = "<Replace with API Base URL from Developer portal>/v1/ping";
        public static string api_gateway_key = "<Replace Api Key from Developer portal>";
        public static string client_id = "<Replace Client Id obtained from Developer portal>";
        public static string client_secret = "<Replace Client Secret from Developer portal>";
 
//Main Method
        static void Main(string[] args)
        {
            using (var httpClient = new HttpClient())
            {
                Task<string> result = getAuthToken(httpClient);
                var authToken = result.Result;
                var pingStatus = getPingResponse(httpClient, authToken);
                //console.Write(pingStatus.Result);
            }
        }
 
//Method to get Auth token
        private static async Task<string> getAuthToken(HttpClient httpClient)
        {
            var data = new Dictionary<string, string>() {
                {
                "grant_type",
                "client_credentials"},
                {
                "client_id",
                client_id},
                {
                "client_secret",
                client_secret}};
 
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", api_gateway_key);
            var response = await httpClient.PostAsync(auth_token_get_url, new FormUrlEncodedContent(data));
            var contents = await response.Content.ReadAsStringAsync();
            AuthToken token = JsonSerializer.Deserialize<AuthToken>(contents);
            return token.access_token;
        }
//Method to get Ping response
        private static async Task<string> getPingResponse(HttpClient httpClient, string authToken)
        {
            httpClient.DefaultRequestHeaders.Accept.Clear();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", authToken);
            httpClient.DefaultRequestHeaders.Add("x-api-key", api_gateway_key);
            var response = Task.Run(() => httpClient.GetAsync(api_url, HttpCompletionOption.ResponseHeadersRead)).Result;
            var contents = await response.Content.ReadAsStringAsync();
            return contents;
        }
 
    }
//Auth Token structure
    public className AuthToken
    {
        public string access_token { get; set; }
        //Token expiration duration is available as part of token and is represented in seconds
        public int expires_in { get; set; }
        public string token_type { get; set; }
        public int not_before_policy { get; set; }
        public string scope { get; set; }
    }
}
```

```mdx-code-block
</TabItem>
</Tabs>
```
