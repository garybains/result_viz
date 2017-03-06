{ // Global intitalization 
	var margin = {top : 30, left : 30, right : 0, bottom : 50};

	var height = 500 - margin.top - margin.bottom;
	var yScale = d3.scale.linear();
	yScale.range([0, height * .9]);

	var width = 1100 - margin.left - margin.right;
	var xScale = d3.scale.linear();
	xScale.range([0, width]);

	var sub_data_base;
	var stu_data_base;
	var year_id = -1;
	var branch_id = -1;
	var subject_id = -1;
	var pass_percent;
	var disti_percent;
}

function get_data() {
	d3.json("static/data/sub" + year_id + ".json", function (data) {
		sub_data_base = data;
		
		d3.json("static/data/stu" + year_id + ".json", function (data) {
			stu_data_base = data;
			if(year_id === 1){
				set_branch(0);
			}
		})
	})
}

function remove_graph() {
	d3.select("#graph")
	.remove();
}

function remove_ring_chart() {
	d3.selectAll(".ring_chart_class")
	.remove();
}

function set_domain(data_array) {
	xScale.domain([0, data_array.length]);
	yScale.domain([0, d3.max(data_array)]);
}

function draw_sub_graph(data_array) {

	remove_graph();

	set_domain(data_array);

	var xAxis = d3.svg.axis()
				.scale(xScale)
				.orient("bottom");

	$("#graphContainer").text("");

	var svg = d3.select("#graphContainer")
				.append("svg")
				.attr("width", width + margin.left) 
				.attr("height", height ) // a little extra is always welcomed
				.attr("id", "graph");

	svg.append("g")
		.attr("class", "axis")
		.attr("transform", "translate(" + (margin.left/5) + ", " + (height - margin.top) + ")")
		.call(xAxis);

	svg.selectAll("rect")
		.data(data_array)
		.enter()
		.append("rect")
		.attr("x", function (d, i) {
			return( margin.left/5 + i*( width / data_array.length) );
		})
		.attr("width", function () {
			return( width/(data_array.length + 15) )
		})
		.attr("y", function (d, i) {
			return( height - yScale(d) - margin.top)
		})
		.attr("height", function (d, i) {
			return( yScale(d) );
		})
		.attr("fill", function (d, i) {
			if ( i >= 0.66 * (data_array.length - 1) ) {
				return("green");
			} else if ( i < 0.4 * (data_array.length - 1) ) {
				return("red");
			} else {
				return("blue");
			}
		})
		.on("mouseover", function(d, i) {
   			d3.select(this)
   				.style("fill", "orange");

			var xPosition = parseFloat(d3.select(this).attr("x")) + 50 / 2;
			var yPosition = parseFloat(d3.select(this).attr("y")) / 2 + 30 + height / 5;
			//Update the tooltip position and value
			var tool_tip = d3.select("#tooltip")
								.style("left", xPosition + "px")
								.style("top", yPosition + "px")						
				
			tool_tip.select("#stu_tip")
					.text("\t" + d + "\t");

			tool_tip.select("#marks_tip")
					.text("\t" + i + "\t");
			//Show the tooltip
			d3.select("#tooltip").classed("hidden", false);
   		})
   		.on("mouseout", function(d, i) {
   			d3.select(this)
   				.transition()
   				.duration(250)
   				.style("fill", function () {
					if ( i >= 0.66 * (data_array.length - 1) ) {
						return("green");
					} else if ( i < 0.4 * (data_array.length - 1) ) {
						return("red");
					} else {
						return("blue");
					}
				});

   			d3.select("#tooltip").classed("hidden", true);
   		});

	set_main_headding();
	//draw_ring_chart();
}

function calc_pass_disti(data_array) {
	var pass_count = 0;
	var fail_count = 0;
	var disti_count = 0;
	for( var stu_i = 0; stu_i < stu_data_base.length; stu_i++ ) {
		for ( var sub_j = 0; sub_j < stu_data_base[stu_i]['subjects'].length; sub_j++ ) {
			if ( stu_data_base[stu_i]['subjects'][sub_j]['code'] === sub_data_base[subject_id]['code'] ) {
				if ( stu_data_base[stu_i]['subjects'][sub_j]['is_pass'] ) {
					pass_count++;
				} else {
					fail_count++;
				}
				if ( stu_data_base[stu_i]['subjects'][sub_j]['obtained_marks'] >= .66 * (sub_data_base[subject_id]['obtained_marks_list'].length - 1)) {
					disti_count++;
				}
			}
		}
	}
	pass_percent = (pass_count*100) / (pass_count + fail_count);
	pass_percent = d3.round(pass_percent, 2);
	disti_percent = (disti_count*100) / (pass_count + fail_count);
	disti_percent = d3.round(disti_percent, 2);
	//console.log("pass_percent = " + pass_percent);
}

function draw_ring_chart() {

	remove_ring_chart();
	calc_pass_disti();

	var w = 500;
	var h = 100;

	var dataset = [pass_percent, (100 - pass_percent)];

	var outerRadius = h / 2;
	var innerRadius = h / 3;
	var arc = d3.svg.arc()
					.innerRadius(innerRadius)
					.outerRadius(outerRadius);
	
	var pie = d3.layout.pie();
	
	//Easy colors accessible via a 10-step ordinal scale
	var color = d3.scale.category10();

	//Create SVG element
	var svg = d3.select("#ringChartPass")
				.append("svg")
				.attr("width", w)
				.attr("height", h)
				.attr("class", "ring_chart_class");

	var arcs = svg.selectAll("g.arc")
				  .data(pie(dataset))
				  .enter()
				  .append("g")
				  .attr("class", "arc")
				  .attr("transform", "translate(" + outerRadius + "," + outerRadius + ")");
	
	//Draw arc paths
	arcs.append("path")
	    .attr("fill", function(d, i) {
	    	if (i === 0) {
	    		return "green";
	    	} else {
	    		return "red"
	    	}
	    })
	    .attr("d", arc);
	
	//Labels
	arcs.append("text")
	    .attr("transform", function(d) {
	    	return "translate(" + -27 + "," + 10 + ")";
	    })
	    .text(dataset[0] + '\xa0' + "%" + '\xa0' + '\xa0' + '\xa0' + "Passed");

	////////////////////////////////////////////////////////////////////////////////
	dataset = [disti_percent, (100 - disti_percent)];
	var svg = d3.select("#ringChartDisti")
				.append("svg")
				.attr("width", w)
				.attr("height", h)
				.attr("class", "ring_chart_class");

	var arcs = svg.selectAll("g.arc")
				  .data(pie(dataset))
				  .enter()
				  .append("g")
				  .attr("class", "arc")
				  .attr("transform", "translate(" + outerRadius + "," + outerRadius + ")");
	
	//Draw arc paths
	arcs.append("path")
	    .attr("fill", function(d, i) {
	    	return color(i);
	    })
	    .attr("d", arc);
	
	//Labels
	arcs.append("text")
	    .attr("transform", function(d) {
	    	return "translate(" + -27 + "," + 10 + ")";
	    })
	    .text(dataset[0] + '\xa0' + "%" + '\xa0' + '\xa0' + '\xa0' + "Got Distinction");
}

function set_main_headding() {
	
	var branch_temp = ""
	if (branch_id === 0) {
		branch_temp = "";
	} else if (branch_id === 1) {
		branch_temp = "Mech";
	} else if (branch_id === 2) {
		branch_temp = "E&TC";
	} else if (branch_id === 3) {
		branch_temp = "Comp";
	} else if (branch_id === 4) {
		branch_temp = "IT";
	} else {
		console.log("ERROR on its way ;(");
	};

	var year_temp = "";
	if (year_id === 1) {
		year_temp = "FE";
	} else if (year_id === 2) {
		year_temp = "SE";
	} else if (year_id === 3) {
		year_temp = "TE";
	} else if (year_id === 4) {
		year_temp = "BE";
	} else {
		console.log("ERROR on its way ;axis");
	};
	
	$("#main_headding").text(sub_data_base[subject_id]['name']);
	$("#main_headding").append("<sub><small>" + sub_data_base[subject_id]['type'] + "</small></sub>");

	var aux_headding_text = "<small>" + "&nbsp&nbsp" + "" + "&nbsp"+ year_temp + "&nbsp" + branch_temp + "</small>";
	$("#main_headding").append(aux_headding_text);
}

function set_year(y) {
	year_id = y;
	if (year_id === 1){
		$("#branch_drop_down").addClass("disabled");
		$("#branch_drop_down_anchor").addClass("disabled");
		branch_id = 0;
	} else {
		$("#branch_drop_down").removeClass("disabled");
		$("#branch_drop_down_anchor").removeClass("disabled");
		$("#subject_drop_down").addClass("disabled");
		$("#subject_drop_down_anchor").addClass("disabled");
	}

	branch_id = -1;
	$("#total_btn").addClass("disabled");
	get_data();

	call("Year_=_" + y);
}

function set_branch(b) {
	branch_id = b;
	set_subjects_drop_down();
	$("#subject_drop_down").removeClass("disabled");
	$("#subject_drop_down_anchor").removeClass("disabled");
	
	subject_id = -1;
	$("#total_btn").addClass("disabled");

	call("Branch_=_" + b);
}

function set_subject(s) {
	subject_id = s;
	var i = 0;
	while( sub_data_base[i].branch_id !== branch_id ) {
		i++;
	}
	subject_id += i;

	draw_sub_graph_type("total");
	draw_ring_chart();
	draw_student_list("total");

	$("#total_btn").removeClass("disabled");
	$("#stu_small_heading").text("Total");
	//console.log(sub_data_base[i]['oe_marks_list'] + "\n" + sub_data_base[i]['th_marks_list']);

	call("Subject_=_" + sub_data_base[subject_id]['name']);
}

function draw_sub_graph_type(graph_of) {

	if ( sub_data_base[subject_id]['oe_marks_list'] === undefined ) {
		$("#online_btn").addClass("disabled");
	} else {
		$("#online_btn").removeClass("disabled");
	}
	if ( sub_data_base[subject_id]['th_marks_list'] === undefined ) {
		$("#theory_btn").addClass("disabled");
	} else {
		$("#theory_btn").removeClass("disabled");
	}

	if ( graph_of === "total" ) {
		draw_sub_graph(sub_data_base[subject_id]['obtained_marks_list']);
		$("#total_btn").addClass("btn-primary");
		$("#online_btn").removeClass("btn-primary");
		$("#theory_btn").removeClass("btn-primary");
	} else if ( graph_of === "online" ) {
		if ( sub_data_base[subject_id]['oe_marks_list'] != undefined ) {
			draw_sub_graph(sub_data_base[subject_id]['oe_marks_list']);
			$("#total_btn").removeClass("btn-primary");
			$("#online_btn").addClass("btn-primary");
			$("#theory_btn").removeClass("btn-primary");
		} 
	} else if (graph_of === "theory") {
		if ( sub_data_base[subject_id]['th_marks_list'] != undefined ) {
			draw_sub_graph(sub_data_base[subject_id]['th_marks_list']);
			$("#total_btn").removeClass("btn-primary");
			$("#online_btn").removeClass("btn-primary");
			$("#theory_btn").addClass("btn-primary");
		}
	}

	draw_student_list(graph_of);
	graph_of = graph_of.charAt(0).toUpperCase() + graph_of.slice(1);
	$("#stu_small_heading").text(graph_of);
	//console.log("here " + sub_data_base[subject_id]);
}

function set_subjects_drop_down() {
	for(var i = 0; i < sub_data_base.length; i++) {
		temp_branch = sub_data_base[i]['branch'];
		if (temp_branch === "Mechanical") {
			sub_data_base[i].branch_id = 1;
		} else if (temp_branch === "Electronics & Telecommu." || temp_branch === "Electronics &telecom") {
			sub_data_base[i].branch_id = 2;
		} else if (temp_branch === "Computer") {
			sub_data_base[i].branch_id = 3;
		} else if (temp_branch === "Information Technology" || temp_branch === "Informatiom Technology") {
			sub_data_base[i].branch_id = 4;
		} else {
			sub_data_base[i].branch_id = 0;
		}
	}
	subjects = [];
	for ( var i = 0; i < sub_data_base.length; i++ ) {
		if ( sub_data_base[i]['branch_id'] === branch_id ) {
			//console.log("si");
			subjects.push(sub_data_base[i]['name'] + " " + sub_data_base[i]['type']);
		}
		else {
			//console.log("nign");
		}
	}

	$(".subject_list").detach();
	//console.log("\tbranch_id = " + branch_id);
	//console.log("\nsubjects = " + subjects);
	for (var i = 0; i < subjects.length; i++) {
		$("#subject_drop_down_list").append("<li><buttonc onclick=\"set_subject(" + i +")\" class=\"subject_list btn btn-link\">" + subjects[i] + "</button></li>");
	}
}

function remove_student_list() {

	$("#studentList").text("");
}

function draw_student_list(list_of) {

	var stu_list = [];
	var temp_stu = {};

	if (list_of === "total") {
		list_of = 'obtained_marks';
	} else if (list_of === "online") {
		list_of = 'oe_marks';
	} else if (list_of === "theory") {
		list_of = 'th_marks';
	}

	if (list_of === 'obtained_marks' || ((sub_data_base[subject_id]['oe_marks_list'] != undefined) && (sub_data_base[subject_id]['oe_marks_list'].length > 10)) ) {
		
		remove_student_list();

		for (var stu_i = 0; stu_i < stu_data_base.length; stu_i++) {
			for ( var sub_j = 0; sub_j < stu_data_base[stu_i]['subjects'].length; sub_j++ ) {
				if ( stu_data_base[stu_i]['subjects'][sub_j]['code'] === sub_data_base[subject_id]['code'] ) {
					temp_stu = {};
					temp_stu['name'] = stu_data_base[stu_i]['student_name'];
					temp_stu['marks'] = stu_data_base[stu_i]['subjects'][sub_j][list_of];
					temp_stu['perma_reg_num'] = stu_data_base[stu_i]['perma_reg_num'];
					stu_list.push(temp_stu);
				}
			}
		}
		
		stu_list.sort(function(x, y) {
			return (y['marks'] - x['marks']);
		});

		var stu_row = "";
		var bar_width = 0;
		var max_width = 500;

		for (var stu_i = 0; stu_i < stu_list.length; stu_i++) {

			bar_width = max_width * (stu_list[stu_i]['marks'] / stu_list[0]['marks']);

			var modal_btn = "<button type=\"button\" onclick=\"set_modal_data('"
								+ stu_list[stu_i]['perma_reg_num']
								+ "')\" class=\"btn btn-link btn-xs\" data-toggle=\"modal\" data-target=\"#myModal\">"
								+ stu_list[stu_i]['perma_reg_num'] +"</button>";

			if ( stu_list[stu_i]['marks'] >= .66 * (sub_data_base[subject_id][(list_of + '_list')].length - 1) ) {

				stu_row = "<tr class=\"success\"><td>" + (stu_i + 1) +"</td><td>" + stu_list[stu_i]['name'] + "</td><td>" + stu_list[stu_i]['marks'] + "</td>";
				stu_row += "<td>" + modal_btn +"</td>";
				stu_row += "<td><svg width=\"" + max_width + "\" height=\"20\">";
				stu_row += "<rect x=\"0\" y=\"0\" width=\"" + max_width + "\" height=\"20\" fill=\"white\"/>"
				stu_row += "<rect x=\"0\" y=\"0\" width=\"" + bar_width + "\" height=\"20\" fill=\"green\"/></svg></td></tr>";

			} else if ( stu_list[stu_i]['marks'] < .4 * (sub_data_base[subject_id][(list_of + '_list')].length - 1) ) {
				
				stu_row = "<tr class=\"danger\"><td>" + (stu_i + 1) +"</td><td>" + stu_list[stu_i]['name'] + "</td><td>" + stu_list[stu_i]['marks'] + "</td>";
				stu_row += "<td>" + modal_btn +"</td>";
				stu_row += "<td><svg width=\"" + max_width + "\" height=\"20\">";
				stu_row += "<rect x=\"0\" y=\"0\" width=\"" + max_width + "\" height=\"20\" fill=\"white\"/>"
				stu_row += "<rect x=\"0\" y=\"0\" width=\"" + bar_width + "\" height=\"20\" fill=\"red\"/></svg></td></tr>";

			} else {
				
				stu_row = "<tr class=\"info\"><td>" + (stu_i + 1) +"</td><td>" + stu_list[stu_i]['name'] + "</td><td>" + stu_list[stu_i]['marks'] + "</td>";
				stu_row += "<td>" + modal_btn +"</td>";
				stu_row += "<td><svg width=\"" + max_width + "\" height=\"20\">";
				stu_row += "<rect x=\"0\" y=\"0\" width=\"" + max_width + "\" height=\"20\" fill=\"white\"/>"
				stu_row += "<rect x=\"0\" y=\"0\" width=\"" + bar_width + "\" height=\"20\" fill=\"blue\"/></svg></td></tr>";
			}
			$("#studentList").append(stu_row);
		}
	}
}

function set_modal_data(perma_reg_num) {

	$("#modal_body").text("");

	var temp_stu_i = -1;
	for (var stu_i = 0; stu_i < stu_data_base.length; stu_i++) {
		if (stu_data_base[stu_i]['perma_reg_num'] === perma_reg_num) {
			temp_stu_i = stu_i;
		}
	}

	$("#modal_title").text(stu_data_base[temp_stu_i]['student_name']);
	$("#modal_title").append("<small>" + '\xa0' + '\xa0' + '\xa0' +  perma_reg_num + "</small>");

	for (var sub_i = 0; sub_i < stu_data_base[temp_stu_i]['subjects'].length; sub_i++) {

		for (var sub_j = 0; sub_j < sub_data_base.length; sub_j++) {
			if (stu_data_base[temp_stu_i]['subjects'][sub_i]['code'] === sub_data_base[sub_j]['code']) {

				var max_width = 300;
				var bar_width = max_width * stu_data_base[temp_stu_i]['subjects'][sub_i]['obtained_marks'] / sub_data_base[sub_j]['obtained_marks_list'].length;
				var color_gradient = d3.round(((250 * bar_width) / max_width), 0) ;

				var temp_var = "<tr><td>" + sub_data_base[sub_j]['name'] + " <small>" + sub_data_base[sub_j]['type'] + "</small></td><td>"
								+ stu_data_base[temp_stu_i]['subjects'][sub_i]['is_pass'] + "</td><td>"
								+ stu_data_base[temp_stu_i]['subjects'][sub_i]['obtained_marks'] + " / " + (sub_data_base[sub_j]['obtained_marks_list'].length - 1) + "</td><td>"
								+ "<svg width=\"" + max_width + "\" height=\"30\">"
								+ "<rect x=\"0\" y=\"5\" width=\"" + max_width + "\" height=\"15\" fill=\"lightgrey\"/>"
								+ "<rect x=\"0\" y=\"5\" width=\"" + bar_width + "\" height=\"15\" fill=\"rgb(" + (255 - color_gradient) + "," + color_gradient + ",0)\"/>";

				if (stu_data_base[temp_stu_i]['subjects'][sub_i]['oe_marks'] != undefined) {
					var online_wtg = 0;
					var theory_wtg = 0;
					if (year_id === 1) {
						online_wtg = .5;
						theory_wtg = .5;
					} else if (year_id === 3) {
						online_wtg = .3;
						theory_wtg = .7;
					} else {
						console.log("ERROR");
					}

					//online marks
					bar_width = online_wtg * max_width * stu_data_base[temp_stu_i]['subjects'][sub_i]['oe_marks'] / sub_data_base[sub_j]['oe_marks_list'].length;
					color_gradient = d3.round((250 * bar_width) / (max_width * online_wtg), 0);

					temp_var += "<rect x=\"0\" y=\"5\" width=\"" + bar_width + "\" height=\"15\" fill=\"rgb(" + (255 - color_gradient) + "," + color_gradient + ", 0)\"/>";

					//theory marks
					var bar_start = bar_width;
					bar_width = theory_wtg * max_width * stu_data_base[temp_stu_i]['subjects'][sub_i]['th_marks'] / sub_data_base[sub_j]['th_marks_list'].length;
					color_gradient = d3.round((250 * bar_width) / (max_width * theory_wtg), 0);
					temp_var += "<rect x=\""+ bar_start +"\" y=\"5\" width=\"" + 1 + "\" height=\"16\" fill=\"rgb(255,255,255)/>"
					temp_var += "<rect x=\""+ (bar_start + 1) +"\" y=\"5\" width=\"" + bar_width + "\" height=\"15\" fill=\"rgb(" + (255 - color_gradient) + "," + color_gradient + ", 0)\"/>";
				}
		
				temp_var += "</svg></td></tr>";

				$("#modal_body").append(temp_var);
				//console.log(temp_var);
			}
		}
	}

	call("Student_=_" + stu_data_base[temp_stu_i]['student_name']);
}

function call(text) {
	// DEBUG
	//$.get(text, function(data, status){
        //console.log(status + "for : " + text);
    });
}

$("#main_headding").text("Result Ananlizer");

call("page_loaded");