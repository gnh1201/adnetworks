<?php
/*
 * api.adnetworks.json.php
 * 
 * Go Namhyeon gnh1201@gmail.com
 * 2020-06-15
 * 
 * ## networks
 * * tenping.kr
 * * api.linkprice.com
 *
 */

loadHelper("webpagetool");

$limit = get_requested_value("limit");
if(empty($limit)) {
	$limit = 15;
}

$tablename = exec_db_table_create(array(
	"hash" => array("varchar", 255),
	"datetime" => array("datetime"),
	"title" => array("varchar", 255),
	"category" => array("varchar", 255),
	"memo" => array("varchar", 255),
	"link" => array("text"),
	"normal_price" => array("int", 20),
	"discount_price" => array("int", 20),
	"network" => array("varchar", 255)
), "adnetwork_items", array(
	"setindex" => array(
		"index_1" => array("hash"),
		"index_2" => array("datetime")
	)
));

// tenping
$response = get_web_json("http://tenping.kr/adbox/list", "get", array(
	"MemberID" => "qa72oCvud/nRRFb92UdVzDZIu0SUT0v2kfQMkS0t+MiMbkZAw9D+O5BCCflZXTru",
	"PageSize" => 10,
	"CampaignType" => 0,
	"MinClickPoint" => 0,
	"MinCurrentPoint" => 0,
	"ExistMiddleImage" => "",
	"ExistRectangleImage" => "",
	"IsPartner" => ""
));

foreach($response->List as $item) {
	$title = $item->ContentTitle;
	$category = $item->CategoryName;
	$memo = $item->ContentMemo;
	$link = $item->Link;
	$hash = get_hashed_text($link);

	$bind = array(
		"hash" => $hash,
		"datetime" => get_current_datetime(),
		"title" => $title,
		"category" => $category,
		"memo" => $memo,
		"link" => $link,
		"normal_price" => 0,
		"discount_price" => 0,
		"network" => "tenping.kr"
	);
	$sql = get_bind_to_sql_insert($tablename, $bind, array(
		"setignores" => array(
			//array("eq", "hash", $hash)
		)
	));
	exec_db_query($sql, $bind);
}


// linkprice hotdeal
$response = get_web_json("https://api.linkprice.com/ci/hotdeal/data/A100666607", "get");
foreach($response as $item) {
	$title = $item->product_name;
	$category = $item->category;
	$memo = $item->promotional_text;
	$link = $item->click_url;
	$hash = get_hashed_text($link);

	$bind = array(
		"hash" => $hash,
		"datetime" => get_current_datetime(),
		"title" => $title,
		"category" => $category,
		"memo" => $memo,
		"link" => $link,
		"normal_price" => $item->normal_price,
		"discount_price" => $item->discount_price,
		"network" => "api.linkprice.com"
	);
	$sql = get_bind_to_sql_insert($tablename, $bind, array(
		"setkeys" => array("hash")
	));
	exec_db_query($sql, $bind);
}

// show list
$data = array();

$bind = false;
$sql = get_bind_to_sql_select($tablename, $bind, array(
	"setorders" => array(
		array("desc", "datetime")
	),
	"setlimit" => $limit,
	"setpage" => 1
));
$rows = exec_db_fetch_all($sql, $bind);
$data['data'] = $rows;

header("Content-Type: application/json");
echo json_encode($data);
