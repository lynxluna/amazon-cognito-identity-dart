# Unofficial Amazon Cognito Identity SDK for Dart
Unofficial Amazon Cognito Identity SDK written in Dart for Dart.

Rewrite of [amazon-cognito-identity-js](https://github.com/aws/aws-amplify/tree/master/packages/amazon-cognito-identity-js) in [Dart](https://www.dartlang.org/).

Please note that this package is _not_ production ready.

## Usage
__Use Case 1.__ Registering a user with the application. One needs to create a CognitoUserPool object by providing a UserPoolId and a ClientId and signing up by using a username, password, attribute list, and validation data.

```dart
import 'package:amazon_cognito_identity_dart/cognito_user_pool.dart';
import 'package:amazon_cognito_identity_dart/attribute_arg.dart';

final userPool = new CognitoUserPool(
  'ap-southeast-1_xxxxxxxxx',
  'xxxxxxxxxxxxxxxxxxxxxxxxxx'
);
final userAttributes = [
  new AttributeArg(name: 'first_name', value: 'Jimmy'),
  new AttributeArg(name: 'last_name', value: 'Wong'),
];

var data;
try {
  data = await userPool.signUp(
    'email@inspire.my',
    'Password001',

    userAttributes: userAttributes, // ..optional
  );
} catch (e) {
  print(e);
}
```

__Use case 2.__ Confirming a registered, unauthenticated user using a confirmation code received via SMS/email.

```dart
import 'package:amazon_cognito_identity_dart/cognito_user_pool.dart';
import 'package:amazon_cognito_identity_dart/cognito_user.dart';

final userPool = new CognitoUserPool(
  'ap-southeast-1_xxxxxxxxx',
  'xxxxxxxxxxxxxxxxxxxxxxxxxx'
);

final cognitoUser = new CognitoUser(
  'email@inspire.my',
  pool,
);

var result;
try {
  result = await cognitoUser.confirmRegistration('123456');
} catch (e) {
  print(e);
}


print(result);
```

__Use case 3.__ Resending a confirmation code via SMS/email for confirming registration for unauthenticated users.

```dart
import 'package:amazon_cognito_identity_dart/cognito_user.dart';
import 'package:amazon_cognito_identity_dart/cognito_user_pool.dart';

final userPool = new CognitoUserPool(
  'ap-southeast-1_xxxxxxxxx',
  'xxxxxxxxxxxxxxxxxxxxxxxxxx'
);
final cognitoUser = new CognitoUser(
  'email@inspire.my',
  pool,
);
final String status;
try {
  status = await cognitoUser.resendConfirmationCode();
} catch (e) {
  print(e);
}
```

__Use case 4.__ Authenticating a user and establishing a user session with the Amazon Cognito Identity service.

```dart
import 'package:amazon_cognito_identity_dart/cognito_user.dart';
import 'package:amazon_cognito_identity_dart/cognito_user_pool.dart';
import 'package:amazon_cognito_identity_dart/cognito_user_session.dart';
import 'package:amazon_cognito_identity_dart/cognito_user_exceptions.dart';

final userPool = new CognitoUserPool(
  'ap-southeast-1_xxxxxxxxx',
  'xxxxxxxxxxxxxxxxxxxxxxxxxx'
);
final cognitoUser = new CognitoUser(
  'email@inspire.my',
  pool,
);
final authDetails = new AuthenticationDetails(
  username: 'email@inspire.my',
  password: 'Password001',
);
CognitoUserSession session;
try {
  session = await cognitoUser.authenticateUser(authDetails);
} on CognitoUserNewPasswordRequiredException catch (e) {
  // handle New Password challenge
} on CognitoUserMfaRequiredException catch (e) {
  // handle SMS_MFA challenge
} on CognitoUserSelectMfaTypeException catch (e) {
  // handle SELECT_MFA_TYPE challenge
} on CognitoUserMfaSetupException catch (e) {
  // handle MFA_SETUP challenge
} on CognitoUserTotpRequiredException catch (e) {
  // handle SOFTWARE_TOKEN_MFA challenge
} on CognitoUserCustomChallengeException catch (e) {
  // handle CUSTOM_CHALLENGE challenge
} on CognitoUserConfirmationNecessaryException catch (e) {
  // handle User Confirmation Necessary
} catch (e) {
  print(e);
}
print(session.getAccessToken().getJwtToken());
```

__Use case 14.__ Signing out from the application.

```dart
cognitoUser.signOut();
```
__Use case 15.__ Global signout for authenticated users (invalidates all issued tokens).

```dart
await cognitoUser.globalSignOut();
```
