# OIDC Integration Guide

To integrate Thakol.io authentication into your applications, you can use any standard OpenID Connect (OIDC) client library. Below are integration examples for popular frameworks and languages.

Want a working starting point instead of snippets? Clone [**thakol-samples**](https://github.com/thakol/thakol-samples) — runnable apps for all seven stacks below.

---

## Before you start

From your Thakol dashboard, grab these values for the app you're integrating:

- **Issuer / Authority** — `https://auth.thakol.io/realms/YOUR_REALM`
- **Client ID** — the OIDC client you register for the app
- **Client Secret** — confidential (server-side) clients only; never expose it in a browser
- **Redirect URI** — must exactly match a value registered on the client

Everything else is discoverable at `<issuer>/.well-known/openid-configuration`, so most libraries only need the issuer.

- **SPAs & mobile** (Vanilla JS, React, Angular) use Authorization Code + PKCE with a **public** client — no secret. Set **Web Origins** to your exact origin (no trailing slash).
- **Backends** (Next.js, Express, FastAPI, Go) use a **confidential** client and validate incoming JWTs against `jwks_uri`, checking the `iss`, `aud`, and `exp` claims.

---

## 🌐 1. Vanilla JavaScript (Single Page Applications)

For vanilla JS, use the community-supported [`oidc-client-ts`](https://github.com/authts/oidc-client-ts) library.

### Installation:
```bash
npm install oidc-client-ts
```

### Configuration & Client Code:
```javascript
import { UserManager } from "oidc-client-ts";

const userManager = new UserManager({
  authority: "https://auth.thakol.io/realms/YOUR_REALM",
  client_id: "your-client-id",
  redirect_uri: window.location.origin + "/callback",
  post_logout_redirect_uri: window.location.origin,
  response_type: "code",
  scope: "openid profile email",
  automaticSilentRenew: true,
});

// 1. Redirect users to sign in
export function login() {
  return userManager.signinRedirect();
}

// 2. Process callback on your redirect URL page
export async function handleCallback() {
  const user = await userManager.signinRedirectCallback();
  console.log("Decoded Profile:", user.profile);
  console.log("Access Token JWT:", user.access_token);
  return user;
}

// 3. Retrieve current session user
export async function getUser() {
  return await userManager.getUser();
}

// 4. Redirect users to sign out
export function logout() {
  return userManager.signoutRedirect();
}
```

---

## ⚛️ 2. React.js

You can wrap the `UserManager` inside a React Context to manage authentication state across your application.

### Reusable Auth Hook:
```javascript
import { useState, useEffect, createContext, useContext } from "react";
import { UserManager } from "oidc-client-ts";

const AuthContext = createContext(null);

const userManager = new UserManager({
  authority: "https://auth.thakol.io/realms/YOUR_REALM",
  client_id: "your-client-id",
  redirect_uri: window.location.origin + "/callback",
  response_type: "code",
  scope: "openid profile email",
});

export function AuthProvider({ children }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    userManager.getUser().then((u) => {
      setUser(u);
      setLoading(false);
    });
  }, []);

  const login = () => userManager.signinRedirect();
  const logout = () => userManager.signoutRedirect();

  return (
    <AuthContext.Provider value={{ user, loading, isAuthenticated: !!user, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

export function useAuth() {
  return useContext(AuthContext);
}
```

---

## 🅰️ 3. Angular

Use the [`angular-oauth2-oidc`](https://github.com/manfredsteyer/angular-oauth2-oidc) library to manage authentication flow in Angular apps.

### Installation:
```bash
npm install angular-oauth2-oidc --save
```

### Step A: Configure Config (`auth.config.ts`)
```typescript
import { AuthConfig } from 'angular-oauth2-oidc';

export const authCodeFlowConfig: AuthConfig = {
  issuer: 'https://auth.thakol.io/realms/YOUR_REALM',
  redirectUri: window.location.origin + '/index.html',
  clientId: 'your-client-id',
  responseType: 'code',
  scope: 'openid profile email',
  showDebugInformation: false,
};
```

### Step B: Configure AppModule (`app.module.ts`)
```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { HttpClientModule } from '@angular/common/http';
import { OAuthModule } from 'angular-oauth2-oidc';
import { AppComponent } from './app.component';
import { authCodeFlowConfig } from './auth.config';

@NgModule({
  declarations: [AppComponent],
  imports: [
    BrowserModule,
    HttpClientModule,
    OAuthModule.forRoot({
      resourceServer: {
        allowedUrls: ['https://api.yourdomain.com'],
        sendAccessToken: true
      }
    })
  ],
  providers: [],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

### Step C: Initialize Auth (`app.component.ts`)
```typescript
import { Component } from '@angular/core';
import { OAuthService } from 'angular-oauth2-oidc';
import { authCodeFlowConfig } from './auth.config';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
})
export class AppComponent {
  constructor(private oauthService: OAuthService) {
    this.oauthService.configure(authCodeFlowConfig);
    this.oauthService.loadDiscoveryDocumentAndTryLogin();
  }

  login() {
    this.oauthService.initCodeFlow();
  }

  logout() {
    this.oauthService.logOut();
  }

  get givenName() {
    const claims = this.oauthService.getIdentityClaims();
    if (!claims) return null;
    return claims['name'] || claims['given_name'];
  }

  get isAuthenticated() {
    return this.oauthService.hasValidIdToken() && this.oauthService.hasValidAccessToken();
  }
}
```

---

## ⚡ 4. Next.js (App Router)

Integrate OIDC in Next.js using [**NextAuth.js (Auth.js)**](https://next-auth.js.org).

### Installation:
```bash
npm install next-auth
```

### Configure Keycloak Provider (`app/api/auth/[...nextauth]/route.ts`):
```typescript
import NextAuth from "next-auth";
import KeycloakProvider from "next-auth/providers/keycloak";

const handler = NextAuth({
  providers: [
    KeycloakProvider({
      clientId: process.env.KEYCLOAK_CLIENT_ID!,
      clientSecret: process.env.KEYCLOAK_CLIENT_SECRET!,
      issuer: process.env.KEYCLOAK_ISSUER!, // e.g. https://auth.thakol.io/realms/YOUR_REALM
    }),
  ],
  callbacks: {
    async jwt({ token, account }) {
      if (account) {
        token.accessToken = account.access_token;
        token.idToken = account.id_token;
      }
      return token;
    },
    async session({ session, token }) {
      (session as any).accessToken = token.accessToken;
      return session;
    },
  },
});

export { handler as GET, handler as POST };
```

---

## 🟢 5. Node.js (Express)

For Express servers, secure web pages using `express-openid-connect` and validate API token headers via `jsonwebtoken` and `jwks-rsa`.

### Installation:
```bash
npm install express express-openid-connect jsonwebtoken jwks-rsa
```

### OIDC Sessions & API Bearer Token Validation (`app.js`):
```javascript
const express = require("express");
const { auth, requiresAuth } = require("express-openid-connect");
const jwt = require("jsonwebtoken");
const jwksClient = require("jwks-rsa");

const app = express();

// A. Session-Based Web Auth
app.use(
  auth({
    authRequired: false,
    issuerBaseURL: "https://auth.thakol.io/realms/YOUR_REALM",
    baseURL: "http://localhost:3000",
    clientID: "your-client-id",
    clientSecret: "your-client-secret",
    secret: "a-very-long-random-signing-secret",
    authorizationParams: {
      response_type: "code",
      scope: "openid profile email",
    },
  })
);

// Protected Web Page
app.get("/profile", requiresAuth(), (req, res) => {
  res.json(req.oidc.user);
});

// B. Stateless API Token Validation Middleware
const client = jwksClient({
  jwksUri: "https://auth.thakol.io/realms/YOUR_REALM/protocol/openid-connect/certs",
});

function getKey(header, callback) {
  client.getSigningKey(header.kid, (err, key) => {
    callback(null, key.publicKey || key.rsaPublicKey);
  });
}

function verifyApiToken(req, res, next) {
  const token = req.headers.authorization?.split(" ")[1];
  if (!token) return res.status(401).json({ error: "Unauthorized" });

  jwt.verify(
    token, 
    getKey, 
    {
      algorithms: ["RS256"],
      issuer: "https://auth.thakol.io/realms/YOUR_REALM"
    }, 
    (err, decoded) => {
      if (err) return res.status(401).json({ error: "Invalid token" });
      req.user = decoded;
      next();
    }
  );
}

// Protected API Endpoint
app.get("/api/data", verifyApiToken, (req, res) => {
  res.json({ secureData: true, user: req.user });
});

app.listen(3000);
```

---

## 🐍 6. Python (FastAPI)

Secure your Python backends using standard OIDC middleware integrations via `authlib`.

### Installation:
```bash
pip install fastapi authlib httpx
```

### FastAPI Configuration:
```python
from fastapi import FastAPI, Request
from fastapi.responses import RedirectResponse
from authlib.integrations.starlette_client import OAuth
from starlette.middleware.sessions import SessionMiddleware

app = FastAPI()
app.add_middleware(SessionMiddleware, secret_key="your-secret-key")

oauth = OAuth()
oauth.register(
    name="thakol",
    client_id="your-client-id",
    client_secret="your-client-secret",
    server_metadata_url="https://auth.thakol.io/realms/YOUR_REALM/.well-known/openid-configuration",
    client_kwargs={"scope": "openid profile email"},
)

@app.get("/login")
async def login(request: Request):
    redirect_uri = request.url_for("callback")
    return await oauth.thakol.authorize_redirect(request, redirect_uri)

@app.get("/callback")
async def callback(request: Request):
    token = await oauth.thakol.authorize_access_token(request)
    user = token.get("userinfo")
    request.session["user"] = dict(user)
    return RedirectResponse(url="/dashboard")

@app.get("/dashboard")
async def dashboard(request: Request):
    user = request.session.get("user")
    if not user:
        return RedirectResponse(url="/login")
    return {"message": f"Hello {user['name']}", "email": user["email"]}
```

---

## 🐹 7. Go

Authenticate users in Go using `go-oidc` and standard oauth2.

### Installation:
```bash
go get golang.org/x/oauth2
go get github.com/coreos/go-oidc/v3/oidc
```

### Go Server Code:
```go
package main

import (
	"context"
	"log"
	"net/http"

	"github.com/coreos/go-oidc/v3/oidc"
	"golang.org/x/oauth2"
)

var (
	clientID     = "your-client-id"
	clientSecret = "your-client-secret"
	redirectURL  = "http://localhost:8080/callback"
	realmURL     = "https://auth.thakol.io/realms/YOUR_REALM"
)

type AuthHandler struct {
	verifier    *oidc.IDTokenVerifier
	oauthConfig oauth2.Config
}

func main() {
	ctx := context.Background()
	provider, err := oidc.NewProvider(ctx, realmURL)
	if err != nil {
		log.Fatalf("Failed to query OIDC provider: %v", err)
	}

	verifier := provider.Verifier(&oidc.Config{ClientID: clientID})

	config := oauth2.Config{
		ClientID:     clientID,
		ClientSecret: clientSecret,
		Endpoint:     provider.Endpoint(),
		RedirectURL:  redirectURL,
		Scopes:       []string{oidc.ScopeOpenID, "profile", "email"},
	}

	handler := &AuthHandler{verifier: verifier, oauthConfig: config}

	http.HandleFunc("/login", handler.HandleLogin)
	http.HandleFunc("/callback", handler.HandleCallback)
	log.Fatal(http.ListenAndServe(":8080", nil))
}

func (h *AuthHandler) HandleLogin(w http.ResponseWriter, r *http.Request) {
	http.Redirect(w, r, h.oauthConfig.AuthCodeURL("random-state-string"), http.StatusFound)
}

func (h *AuthHandler) HandleCallback(w http.ResponseWriter, r *http.Request) {
	ctx := context.Background()

	// Exchange code for tokens
	code := r.URL.Query().Get("code")
	oauth2Token, err := h.oauthConfig.Exchange(ctx, code)
	if err != nil {
		http.Error(w, err.Error(), http.StatusInternalServerError)
		return
	}

	// Verify ID Token signature using JWKS
	rawIDToken := oauth2Token.Extra("id_token").(string)
	idToken, err := h.verifier.Verify(ctx, rawIDToken)
	if err != nil {
		http.Error(w, "Invalid token: "+err.Error(), http.StatusUnauthorized)
		return
	}

	var claims struct {
		Email string `json:"email"`
		Name  string `json:"name"`
	}
	_ = idToken.Claims(&claims)

	w.Write([]byte("Successfully logged in: " + claims.Name))
}
```
