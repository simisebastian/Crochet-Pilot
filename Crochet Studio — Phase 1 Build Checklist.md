# Crochet Studio — Phase 1 Build Checklist

Oct 25, 2026 · @Simi Sebastian

This checklist takes Phase 1 from an empty folder to a closed test on Google Play, in the order that removes the biggest risks first: decisions and accounts, then sign-in, then the timer, then the rest. Three findings from current documentation affect your budget and timeline, so they come first.

## Check these first

| Topic | What the documentation says | What to do |
| --- | --- | --- |
| Photo storage cost | Cloud Storage for Firebase now needs the pay-as-you-go Blaze plan, which means linking a billing account. No-cost usage is still available on Blaze within the free quota. Firestore and standard sign-in can stay on the no-cost Spark plan. | Decide whether photos are in the MVP (Stage 0). |
| Google Play testing rule | A personal developer account created after 13 November 2023 must run a closed test with at least 12 opted-in testers for 14 continuous days before applying for production access. Organization accounts are exempt, but need a D-U-N-S number, which can take weeks. | Line up 12 or more testers now (interview question 26). Plan about three weeks for a first release. |
| Ionic and Angular versions | Ionic 8 documents Angular support up to Angular 20. Angular 21 support was being added for Ionic 9 as of June 2026. I could not confirm whether Ionic 9 has been released. | When you create the project, check Ionic's framework support table and use the newest combination it officially supports, even if it is not the Angular version you use at work. |

## Stage 0: Decisions before coding

- **Versions:** choose the Ionic, Angular and Capacitor combination as described above.
- **Photos in the MVP?** Three options:
  - A: include photos, use the Blaze plan, set a budget alert in Google Cloud billing, compress images on the phone, and limit photos per project.
  - B: keep photos on the device only. There is no cloud cost, but photos are not backed up and do not move to a new phone.
  - C: leave photos out of the MVP and add them later.
  - My suggestion: A if you are comfortable linking a billing account with a budget alert, otherwise C. B is the least attractive because users lose their photos with the phone.
- **Minimum Android version:** an open question in the requirements document.
- **App name and package ID:** the package ID (for example com.yourname.crochetstudio) cannot be changed after publishing, so choose it carefully. Check that the name "Crochet Studio" is free on Google Play.
- **Play account type:** personal (needs the 12-tester closed test) or organization.

## Stage 1: Accounts and tools

- Create a Firebase project on the Spark plan.
- Start the Google Play Console developer registration early, because identity verification can take time. Check the current one-time fee.
- Install Node (LTS version), Android Studio, the Ionic CLI and Git.
- Turn on USB debugging on a real Android phone.

**Done when:** a blank Ionic app runs on your phone.

## Stage 2: App skeleton

- Create an Ionic Angular project (standalone, blank starter) with the supported versions, add the Capacitor Android platform, and run it on the device.
- Set the app ID and name in the Capacitor config.
- Organise code by feature: auth, projects, timer, collection, profile, plus a core folder for data access and models.
- Keep Firebase config in environment files and set up linting, formatting and a Git repository.
- Build the bottom tab layout with empty pages: Home, Projects, Collection, Profile.

**Done when:** the tabs navigate on your phone.

## Stage 3: Firebase and Google sign-in

- Add an Android app in Firebase with your package ID and put the google-services.json file in the Android project.
- Add your debug key's SHA-1 fingerprint in Firebase project settings, and enable Google as a sign-in provider in Authentication.
- Install AngularFire and a maintained Capacitor plugin for Firebase authentication. Check the plugin's last release and Capacitor compatibility first, because several older plugins that appear in searches target old Capacitor versions.
- Build the sign-in screen, sign-out, a guard on the tabs, and a session that survives app restarts.
- Build the optional studio name step shown after the first sign-in.
- Test on a real device with two different Google accounts.
- Before the closed test, add the release key's SHA-1 and, if you use Play App Signing, the app signing key's SHA-1 shown in Play Console. Otherwise Google sign-in typically fails in the Play build.

**Done when:** you can sign in, close the app, reopen it still signed in, and sign out.

## Stage 4: Data and security

- Model the data as in section 8 of the requirements document: projects, collection items (yarn and hooks), and sessions as a subcollection of each project.
- Turn on Firestore offline persistence and test in airplane mode.
- Write security rules so each user can read and write only their own data, and test them with the Firebase emulator before publishing.
- Calculate a project's total time from its opening time plus its sessions. Avoid storing a total that can drift, at least at first.
- Plan account deletion (AUTH-07): delete the user's documents, then the sign-in account. Firebase may ask for a recent sign-in before it deletes an account.

**Done when:** data created offline appears after reconnecting, and a second account cannot see the first account's data.

## Stage 5: Timer and sessions (the riskiest part)

- Store the session start as a timestamp, never as a counter held in memory (TMR-06).
- Allow only one running timer at a time (TMR-03).
- Show an ongoing notification with a pause action while a timer runs (TMR-07), and check whether battery saver settings stop it.
- On app start, detect a session left running and ask how to end it (TMR-09).
- Add manual add, edit and delete for sessions (TMR-08).
- Write unit tests for the duration calculation.
- Test these cases: kill the app, restart the phone, airplane mode, change the clock or time zone, and leave the app in the background for hours.

**Done when:** every test above gives a correct duration. Do not move on before this is true.

## Stage 6: Projects, collection and photos

- Collection: add, edit and delete yarn and hooks (COL-01 to COL-03).
- Projects: create, edit, delete, stages, notes, pattern link and row counter (WIP-01 to WIP-10).
- Yarn and hook pickers with search and "add new" (COL-04).
- Photos, only if you chose option A: pick from camera or gallery, compress on the phone, upload, and apply the per-project limit.

**Done when:** you can start a new project in under a minute using only your collection.

## Stage 7: Home, Projects tab and Profile

- Home: studio name, hours today and this week, active projects with a Start timer button.
- Projects: stage filter.
- Profile: edit profile, sign out, and delete account and data.

**Done when:** every Phase 1 screen in the design canvas exists and links to the others.

## Stage 8: Quality and store requirements

- Test on two or three real Android devices, including a low-end phone and a small screen.
- Check touch target sizes, text contrast and large font settings.
- Add empty states, clear error messages and an offline indicator.
- Host a privacy policy at a public web address. Google Play expects one for apps that handle personal data. Complete the Data safety and content rating forms in Play Console.
- Optional: add crash reporting (Firebase Crashlytics).

**Done when:** a friend can install and use the app without your help.

## Stage 9: Release to the closed test

- Prepare the app icon, name, short and full description, and screenshots.
- Build a signed Android App Bundle and use Play App Signing.
- Upload to an internal test first, then to a closed test.
- Invite at least 12 testers and remind them to stay opted in for 14 continuous days.
- Collect feedback with a short survey at the end of week 1 and week 2.
- Apply for production access only after you have decided to continue.

## Leave out of Phase 1

Price calculator, catalogue, public pages, community, home-screen timer, phone sign-in and iOS.

## Sources checked

- Google Play Help, "App testing requirements for new personal developer accounts" (support.google.com/googleplay/android-developer/answer/14151465).
- Firebase documentation, FAQ on Cloud Storage for Firebase pricing and default bucket changes (firebase.google.com/docs/storage/faqs-storage-changes-announced-sept-2024).
- Ionic Framework GitHub pull request 31185, "support Angular 21, drop Angular 16 and 17".

Prices, limits and version support change, so check the official pages again before you commit.
