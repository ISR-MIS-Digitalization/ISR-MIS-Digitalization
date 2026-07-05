# Team Git + Apps Script Workflow Guide
### ITREB Attendance Project

Yeh document batata hai ke hamari 3-member team GitHub par code kaise manage karegi —
setup se le kar Pull Request aur Production tak. Simple steps, bas padho aur follow karo.

---

## 1. Big Picture (pehle yeh samajh lo)

Har banda apni **alag Sheet** par code karta hai (taake sab ek saath kaam kar saken).
Saara code **GitHub** par jaata hai. `main` branch hamesha "sahi aur tested" code rakhti hai.

```
Apni Sheet (coding)  →  Branch push  →  Pull Request  →  Asad approve + merge
                                                              │
                                                              ▼
                                                          main branch (GitHub)
                                                              │
                                                              ▼
                                    Deployment Sheet  (sirf PULL — testing yahan)
                                                              │
                                                    Testing OK hone ke baad
                                                              ▼
                                        PROD / Multi-REC  (Asad khud PULL karega)
```

### Team aur Roles

| Naam | Role | GitHub Access | Kaam |
|------|------|---------------|------|
| **Asad** | Manager | Org **Owner / Admin** | Baseline push, rules set, **PR approve + merge**, Prod par pull |
| **Shan** | Member | **Write** | Coding, branch + PR raise |
| **Hunaid** | Member | **Write** | Coding, branch + PR raise |

### Environments (kaun si Sheet kis kaam ki)

| Sheet | Kiske paas | Kaam | Rule |
|-------|-----------|------|------|
| **Asad ki Dev Sheet** | Asad | Coding | Branch par push |
| **Shan ki Dev Sheet** | Shan | Coding | Branch par push |
| **Hunaid ki Dev Sheet** | Hunaid | Coding | Branch par push |
| **Deployment Sheet** | Sab ke paas | Testing | **Sirf PULL** (yahan coding nahi) |
| **PROD (Multi-REC)** | **Sirf Asad** | Live app | **Sirf Asad PULL karega** |

> Yaad rakho: Deployment aur Prod par kabhi coding nahi hoti — wahan sirf `main` ka
> code **pull** hota hai. Coding hamesha apni Dev Sheet par.

---

## 2. One-Time Setup

### PART A — Manager (Asad) ka setup — sabse pehle yeh

Yeh sab Asad apne laptop se karega. Ek hi baar.

**Step 1 — GitHub account + Organization**
1. [github.com](https://github.com) par account banao.
2. Top-right `+` → **New organization** → plan mein **Free ($0)** choose karo.
3. Org ka naam do (e.g. `itreb`).

**Step 2 — Private repository banao**
1. Org ke andar **New repository**.
2. Naam: `itreb-attendance` → **Private** → Create.
   *(MIS ke liye alag repo `itreb-mis` bana lena, wahi steps.)*

**Step 3 — Chrome extension + token**
1. Chrome Web Store → **"Google Apps Script GitHub Assistant"** → Add to Chrome.
2. GitHub token banao: **Settings → Developer settings → Personal access tokens →
   Tokens (classic) → Generate new token** → scope **`repo`** tick → copy karke save karo.
   *(Token dobara nahi dikhta — kahin note kar lo.)*

**Step 4 — Baseline code push**
1. Apni **Dev Sheet** ka Apps Script editor kholo (yehi asli current code hai).
2. Editor ke upar GitHub toolbar → token paste → repo `itreb/itreb-attendance`,
   branch **`main`** select.
3. **Push** → commit message: `baseline v1 - production snapshot`.
4. GitHub par jaa kar verify karo ke files aa gayin.

**Step 5 — Rules set karo (jaise humne kiya)**
1. Repo → **Settings → Rules → Rulesets → New branch ruleset**.
2. Name: `protect-main`, Enforcement: **Active**, Target: **default branch (main)**.
3. Tick karo:
   - ✅ **Require a pull request before merging** → Required approvals: **1**
   - ✅ **Require review from Code Owners**
   - ✅ **Block force pushes**
4. **Bypass list → Add bypass → Repository admin** (taake Asad ki apni PR bina review merge ho).
5. Save.

**Step 6 — CODEOWNERS (sirf Asad approve kare)**
1. Ruleset ko thodi der **Disabled** karo (taake file main par commit ho sake).
2. Repo → **Add file → Create new file** → path: `.github/CODEOWNERS`
3. Content likho:
   ```
   * @asad-github-username
   ```
4. Commit. Phir ruleset wapas **Active** karo.

**Step 7 — Members invite karo**
1. Org → **People → Invite member** → Shan aur Hunaid ko invite.
2. Unhe repo par **Write** access do (Repo → Settings → Collaborators and teams).
   *(Sirf Asad Admin rahega, members Write.)*

✅ Manager ka setup complete.

---

### PART B — Member (Shan / Hunaid) ka setup — har member apne laptop par

**Step 1 — Invite accept karo**
Email / GitHub notification se org ka invite accept karo.

**Step 2 — Apna token banao**
GitHub → **Settings → Developer settings → Personal access tokens → Tokens (classic)**
→ Generate → scope **`repo`** → copy karke save karo.
*(Har banda apna alag token banaye — kabhi share mat karo.)*

**Step 3 — Chrome extension**
Chrome Web Store → **"Google Apps Script GitHub Assistant"** → Add to Chrome.

**Step 4 — Apni Dev Sheet connect karo**
1. Apni **Dev Sheet** ka Apps Script editor kholo.
2. GitHub toolbar → apna token paste → repo `itreb/itreb-attendance` select.

**Step 5 — Latest code le lo**
Branch **`main`** select → **Pull**. Ab tumhare editor mein baseline code aa gaya.

✅ Member ka setup complete. Ab coding ready.

---

## 3. Daily Coding Workflow (har change ke liye — yeh sabse important)

Jab bhi koi change karna ho, **hamesha yeh 6 steps** follow karo:

**Step 1 — Pehle `main` se PULL karo**
Editor mein branch **`main`** select → **Pull**.
*(Taake tum latest code se shuru karo, purane se nahi.)*

**Step 2 — Apni feature branch banao**
Extension mein nayi branch banao. Naam ka format:
```
DeveloperName/short-description
```
Examples:
- `shan/login-rec-fix`
- `hunaid/barcode-scanner-fix`
- `asad/hours-setup-update`

**Step 3 — Code karo**
Apni Dev Sheet ke editor mein change karo. Ek PR = ek chhota focused kaam.

**Step 4 — Apni branch par PUSH karo**
Extension mein apni branch select ho → **Push** → commit message likho.
Commit message chhota aur saaf:
```
fix: login REC verify
```

**Step 5 — Pull Request raise karo**
1. GitHub web par jao → repo → **"Compare & pull request"** button dikhega.
2. Base = **`main`**, Compare = **teri branch**.
3. Title + chhoti description likho → **Create pull request**.

**Step 6 — Approve + Merge**
- **Member ki PR:** Asad review karega → **Approve** → **Merge**.
- **Asad ki apni PR:** woh khud merge kar lega (bypass ki wajah se review nahi chahiye).

Merge ke baad branch delete kar sakte ho (optional).

> **Golden rule:** `main` par kabhi direct push nahi. Hamesha branch → PR → merge.

---

## 4. Testing (Deployment Sheet)

1. Jab PR `main` mein merge ho jaaye, **Deployment Sheet** ka Apps Script editor kholo.
2. Branch **`main`** select → **Pull**. (Sirf pull — yahan coding nahi.)
3. Deploy karke test karo. Yeh Sheet sab ke paas hai.
4. Kuch galat mile to naya PR banao (fix), wahi cycle.

---

## 5. Production Release (Multi-REC)

1. Deployment Sheet par testing OK ho jaaye.
2. **Sirf Asad** Prod (Multi-REC) Sheet ka editor kholega.
3. Branch **`main`** select → **Pull** → deploy.
4. Shan / Hunaid ka Prod par access nahi — sirf Asad release karega.

---

## 6. Golden Rules (yaad rakhne wali baatein)

- ✅ Coding sirf apni **Dev Sheet** par.
- ✅ Kaam se pehle hamesha **`main` se Pull**.
- ✅ Hamesha **branch** banao, `main` par direct push nahi.
- ✅ Branch naam: `DeveloperName/short-desc`.
- ✅ Ek PR = ek chhota focused change.
- ✅ Member PR sirf **Asad** approve karega.
- ✅ **Deployment** aur **Prod** par sirf **Pull** — coding nahi.
- ✅ Prod release sirf **Asad**.

---

## 7. Common Problems (jaldi hal)

| Problem | Hal |
|---------|-----|
| Repo extension mein nahi dikh raha | Token mein `repo` scope check karo, re-login. |
| Push par conflict aaya | Pehle **Pull** karo, phir **Push**. |
| PR par "Review required" | Asad ke approve ka intezaar — member khud approve nahi kar sakta. |
| Merge blocked | Code owner (Asad) ka approval chahiye. |
| Pull ne meri changes mita di | Pull se pehle apna kaam commit/push kar liya karo. |

---

*Is workflow ka maqsad: har change tracked ho, koi bug bina review Prod par na jaaye,
aur teeno log bina ek doosre ko disturb kiye aaram se kaam kar saken.*
