# GEIE Solar Attendance — Android Studio Project

Package: `com.geiesolar.attedence`  
Firebase project: `geiesolar-attendance`  
Admin ID reserved in app UI: `GE0083`

## अभी क्या शामिल है
- Employee ID/password Firebase Authentication login
- Check-in / Check-out events with date, timestamp and last known GPS coordinates
- My attendance history
- Shared solar-site record creation
- Admin-only screens in the app UI for employee directory, employee profile records and monthly event listing
- Hindi/English labels

## जरूरी: यह अभी signed/compiled APK नहीं है
इस workspace में Android SDK/Gradle build environment उपलब्ध नहीं है, इसलिए APK compile/sign नहीं किया गया। यह Android Studio में खोलने योग्य source project है।

## Firebase setup
1. Android Studio में इस folder को खोलें और Gradle sync करें।
2. Firebase Console में Authentication > Sign-in method में Email/Password enable करें।
3. Firestore Database बनाएं और नीचे दिए गए rules लागू करें।
4. हर कर्मचारी के लिए Firebase Authentication user बनाएं। Email format: `EMPLOYEEID@geiesolar.in` (उदाहरण `GE0083@geiesolar.in`) और password दें।
5. Firestore `employees` collection में employee ID document बनाएं।
6. Admin account `GE0083@geiesolar.in` को ही admin रखें।
7. Build > Build APK(s) से APK बनाएं।

## Firestore rules — production में deploy करने से पहले समीक्षा करें
नीचे का नियम signed-in users को attendance और site records लिखने/पढ़ने देता है। Admin authorization को मजबूत करने के लिए Firebase Auth custom claims/Cloud Functions जोड़ना आवश्यक है; केवल ऐप में छिपे Admin buttons सुरक्षा नहीं हैं। इसीलिए इस starter में Admin UI gating को security boundary न मानें।

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    function signedIn() { return request.auth != null; }
    match /attendance_events/{id} {
      allow create: if signedIn();
      allow read: if signedIn();
    }
    match /solar_site_records/{id} {
      allow read, create: if signedIn();
      allow update, delete: if false;
    }
    match /employees/{id} {
      allow read: if signedIn();
      allow write: if false;
    }
  }
}
```

## सीमाएँ जिन्हें अगले build में पूरा करना है
- इस starter में selfie/camera capture और image upload अभी लागू नहीं हैं।
- Admin द्वारा सुरक्षित रूप से employee Auth account/password बनाना Cloud Function या सुरक्षित backend मांगता है।
- Excel/PDF export और offline write queue अभी लागू नहीं हैं।
- Monthly screen अभी event listing है, पूर्ण P/A/WO/L/H गणना वाला payroll attendance sheet नहीं।
- Firestore security rules को production में deploy करने से पहले custom claims आधारित Admin/employee permissions से बदलें।
