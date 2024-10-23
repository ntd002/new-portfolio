<svelte:head>
    <title>Ntd002: Personal site and portfolio</title>
</svelte:head>

<!-- <!DOCTYPE html>
<html lang="en" class="home">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    %sveltekit.head%
    <title>Ntd002: Personal site and portfolio</title>
    <link rel="stylesheet" href="%sveltekit.assets%/style.css" />
	<script src="%sveltekit.assets%/global.js" type="module"></script>
</head>
<body>
    <h1>Nathaniel Do</h1>
    <p>I bake, code, and am painting my house.</p>
    <img class="big_image" src="images/chicken parm.jpg" alt="Chicken Parmesan that looks better than one you've had before">
</body>
</html> -->

<!doctype html>
<html lang="en">
	<head>
		<meta charset="utf-8" />
		<link rel="icon" href="%sveltekit.assets%/favicon.png" />
		<meta name="viewport" content="width=device-width, initial-scale=1" />
		<!-- %sveltekit.head% -->
		<title>Ntd002: Personal site and portfolio</title>
		<link rel="stylesheet" href="%sveltekit.assets%/style.css" />
		<script src="%sveltekit.assets%/global.js" type="module"></script>
	</head>
	<body data-sveltekit-preload-data="hover">
        <div style="display: contents">%sveltekit.body%</div>
		<h1>Nathaniel Do</h1>
        <p>I bake, code, and am painting my house.</p>
        <img class="big_image" src="images/chicken parm.jpg" alt="Chicken Parmesan that looks better than one you've had before">
	</body>
</html>
