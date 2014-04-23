# HTTP Auth for AngularJS

AngularJS service for handling HTTP authentication and request queuing.

---
This library allows Angular applications to connect to APIs which which expose their resources via HTTP authentication.

It does this by waiting for failed HTTP requests, and notifying subscribed scopes that authentication is required. Scopes can prompt users for credentials, which are then submitted to the auth service. When the queue of failed requests is flushed, the requests are re-tried with credentials.

## Example

```javascript
/**
 * Define application module with `ur.http.auth` dependency.
 */
var app = angular.module("app", ["ur.http.auth", "ngResource"]).run(function($resource) {

  /**
   * Query a resource that is protected by authentication.
   */
   var Item = $resource("https://api.myservice.com/items/:id", { id: "@id" });

  $rootScope.items = Item.query();

}).controller("LoginController", function($scope, $dialog, requestQueue, httpAuth) {

  /**
   * Set up the controller to handle the UI for the authentication credentials. The
   * `login()` method should be bound to a login button pressed by the user.
   */
  angular.extend($scope, {
    credentials: {
      username: "",
      password: ""
    },

    /**
     * After the user enters their credentials, submit them to the `httpAuth` service,
     * then flush the failed requests queue.
     */
    login: function() {
      httpAuth.basic(this.credentials);
      requestQueue.flush();
    },

    show: function() {
      /* Show login UI */
    }
  });

  /**
   * Wait for failed requests and prompt for login.
   */
  $scope.$on("requestFailed", function(event) {
    $scope.show();
  });

});
```
