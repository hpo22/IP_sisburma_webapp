# Firebase Setup Instructions

## Prerequisites
You need to have a Firebase account. If you don't have one, sign up at [https://firebase.google.com/](https://firebase.google.com/)

## Step 1: Create a Firebase Project

1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Click "Add project" or "Create a project"
3. Enter a project name (e.g., "sisburma-webapp")
4. Follow the setup wizard (you can disable Google Analytics if not needed)
5. Click "Create Project"

## Step 2: Register Your Web App

1. In your Firebase project dashboard, click the **Web icon** (`</>`) to add a web app
2. Enter an app nickname (e.g., "SISBURMA Web App")
3. Click "Register app"
4. You'll see your Firebase configuration object - **SAVE THIS!**

## Step 3: Enable Realtime Database

1. In the Firebase Console, go to **Build** > **Realtime Database**
2. Click "Create Database"
3. Choose a location (select the one closest to your users)
4. Start in **Test mode** (for development) - this allows read/write access
   - **Important**: Test mode rules expire after 30 days. For production, set proper security rules.
5. Click "Enable"

## Step 4: Configure Your Files

### Update product.html

Open `product.html` and find the Firebase configuration section (around line 393):

```javascript
const firebaseConfig = {
    apiKey: "YOUR_API_KEY",
    authDomain: "YOUR_AUTH_DOMAIN",
    databaseURL: "YOUR_DATABASE_URL",
    projectId: "YOUR_PROJECT_ID",
    storageBucket: "YOUR_STORAGE_BUCKET",
    messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
    appId: "YOUR_APP_ID"
};
```

Replace the placeholder values with your actual Firebase configuration values from Step 2.

### Update cart.html

Open `cart.html` and find the Firebase configuration section (around line 383):

```javascript
const firebaseConfig = {
    apiKey: "YOUR_API_KEY",
    authDomain: "YOUR_AUTH_DOMAIN",
    databaseURL: "YOUR_DATABASE_URL",
    projectId: "YOUR_PROJECT_ID",
    storageBucket: "YOUR_STORAGE_BUCKET",
    messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
    appId: "YOUR_APP_ID"
};
```

Replace with the **same** configuration values from Step 2.

## Step 5: Set Database Rules (Production)

For production, update your Realtime Database rules:

1. Go to **Realtime Database** > **Rules** tab
2. Replace the rules with:

```json
{
  "rules": {
    "carts": {
      "$sessionId": {
        ".read": true,
        ".write": true
      }
    }
  }
}
```

Or for better security (requires authentication):

```json
{
  "rules": {
    "carts": {
      "$sessionId": {
        ".read": "$sessionId === auth.uid",
        ".write": "$sessionId === auth.uid"
      }
    }
  }
}
```

3. Click "Publish"

## Data Structure

Your data in Firebase will be organized as:

```
carts/
  └── user_<timestamp>_<randomId>/
      ├── item1: { name, price, size, color, quantity, image }
      ├── item2: { name, price, size, color, quantity, image }
      └── ...
```

Each user session gets a unique ID stored in `sessionStorage`, and their cart items are stored under that ID in Firebase.

## Testing

1. Open your website in a browser
2. Add items to cart from product.html
3. Check Firebase Console > Realtime Database to see the data being stored
4. Open cart.html to view your cart
5. Try removing items and checking out

## Troubleshooting

### "Permission denied" error
- Make sure your Database Rules allow read/write access
- Check that you're using the correct `databaseURL`

### Data not saving
- Open browser console (F12) to check for errors
- Verify your Firebase configuration is correct
- Ensure you have internet connection

### CORS or loading issues
- Make sure you're accessing the site via a web server (not `file://`)
- You can use Live Server extension in VS Code or run: `python -m http.server 8000`

## Important Notes

- **Session ID**: Cart data is tied to a session ID stored in `sessionStorage`. Closing the tab/browser window will clear the session ID, creating a new session on next visit.
- **Test Mode Expiry**: Test mode security rules expire after 30 days. Update to production rules before going live.
- **No Authentication**: Current implementation uses session-based storage without user authentication. For a production app, consider implementing Firebase Authentication.

## Next Steps (Optional Enhancements)

1. **Add Firebase Authentication** to link carts to actual user accounts
2. **Implement persistent carts** by storing session ID in `localStorage` instead of `sessionStorage`
3. **Add product inventory tracking** in Firebase
4. **Create an admin dashboard** to view all orders
5. **Set up Firebase Hosting** to deploy your website

---

For more information, visit [Firebase Documentation](https://firebase.google.com/docs)
