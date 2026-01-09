## Feature Flags

Feature Flags are used to manage features that are not yet ready for general use. Their behavior varies depending on the branch:

### **main**
Feature flags are enabled once all related code for the feature have landed.

### **beta**
Feature flags remain enabled once the feature is complete unless the developers decide to temporarily pause it.

### **release and esr**
Feature flags are disabled by default until an explicit decision is made to enable the feature for all users.
