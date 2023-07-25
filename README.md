# PDO_prettified-
As mentioned in PDO, I tried to make a simple design<br>

I will use the base of my [PDO Project](https://github.com/SenselessCoding/PDO) and make it look a bit more modern.<br>
Major difference is the formatting, info msg and some basic error handling.<br>
For this we will be using tailwind:<br>
```php
<?php
session_start();
?>

<!doctype html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="p-4">
 <h1 class="text-2xl font-bold mb-4">HELLO WORLD</h1>

  <!-- Form for inserting data -->
  <form class="mb-4" action="insert.php" method="POST">
    <label class="block mb-2 font-bold" for="orderID">Order ID:</label>
    <input class="border border-gray-300 px-4 py-2 w-full" type="text" id="orderID" name="orderID" placeholder="Enter order ID">

    <label class="block mb-2 font-bold" for="orderNumber">Order Number:</label>
    <input class="border border-gray-300 px-4 py-2 w-full" type="text" id="orderNumber" name="orderNumber" placeholder="Enter order number">

    <label class="block mb-2 font-bold" for="personID">Person ID:</label>
    <input class="border border-gray-300 px-4 py-2 w-full" type="text" id="personID" name="personID" placeholder="Enter person ID">

    <button class="bg-blue-500 text-white px-4 py-2 mt-2" type="submit">Insert</button>
  </form>

  <?php
  require_once 'connect.php';

  // Display success message
  if (isset($_SESSION['success'])) {
      echo '<div class="text-green-500 mb-4">' . $_SESSION['success'] . '</div>';
      unset($_SESSION['success']);
  }

  // Display error message
  if (isset($_SESSION['error'])) {
      echo '<div class="text-red-500 mb-4">' . $_SESSION['error'] . '</div>';
      unset($_SESSION['error']);
  }
  ?>

  <!-- Table to display data -->
  <table class="border border-gray-300 w-full">
    <thead>
      <tr>
        <th class="border-b px-4 py-2">Order ID</th>
        <th class="border-b px-4 py-2">Order Number</th>
        <th class="border-b px-4 py-2">Person ID</th>
        <th class="border-b px-4 py-2">Actions</th>
      </tr>
    </thead>
    <tbody>
      <?php
      try {
          $stmt = $pdo->query("SELECT * FROM orders");
          $result = $stmt->fetchAll(PDO::FETCH_ASSOC);
          foreach ($result as $row) {
              echo "<tr>";
              echo "<td class='border-b px-4 py-2'> <input class='border border-gray-300 px-4 py-2 w-full' type='text' id='orderID' name='orderID' placeholder=" . $row['OrderID'] . " /></td>";
              echo "<td class='border-b px-4 py-2'> <input class='border border-gray-300 px-4 py-2 w-full' type='text' id='orderNumber' name='orderNumber' placeholder=" . $row['OrderNumber'] . " /></td>";
              echo "<td class='border-b px-4 py-2'> <input class='border border-gray-300 px-4 py-2 w-full' type='text' id='personID' name='personID' placeholder=" . $row['PersonID'] . " /></td>";
              echo "<td class='border-b px-4 py-2'>
                      <form action='delete.php' method='POST' style='display:inline-block;'>
                          <input type='hidden' name='orderID' value='" . $row['OrderID'] . "'>
                          <button class='bg-red-500 text-white px-2 py-1' type='submit'>Delete</button>
                      </form>
                    </td>";
              echo "</tr>";
          }
      } catch (PDOException $e) {
          $_SESSION['error'] = 'Query failed: ' . $e->getMessage();
      }
      ?>
    </tbody>
  </table>
</body>
</html>
```

For all the other files:<br>
I added try catch for error handling.<br>
f.e.:<br>
```php
<?php
session_start();
require_once 'connect.php';

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $orderID = $_POST['orderID'];

    try {
        $stmt = $pdo->prepare("DELETE FROM orders WHERE OrderID = ?");
        $stmt->execute([$orderID]);
        $_SESSION['success'] = 'Data deleted successfully!';
    } catch (PDOException $e) {
        $_SESSION['error'] = 'Deletion failed: ' . $e->getMessage();
    }

    header('Location: index.php');
    exit();
}
?>
```
