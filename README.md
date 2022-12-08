/* 
 * To change this license header, choose License Headers in Project Properties.
 * To change this template file, choose Tools | Templates
 * and open the template in the editor.
 */
var jpdbBaseURL ='http://api.login2explore.com:5577';
var jpdbIRL = '/api/irl';
var jpdbIML='/api/iml';
var empDBName = "EMP-DB";
var empRelationName="EmpData";
var connToken="90936571|-31948846965960543|90934225";

$('#empid').focus();
 function saveRecNo2LS(jsonObj){
     var lvData=JSON.parse(jsonObj.data);
     localStorage.setItem('recno',lvData.rec_no);
 }
 function getEmpIdAsJsonObj(){
     var empid=$('#empid').val();
     var jsonStr={
     id:empid
     };
     return JSON.stringify(jsonStr);
     
 }
function fillData(jsonObj){
    saveRecNo2LS(jsonObj);
    var record=JSON.parse(jsonObj.data).record;
    $('#empname').val(record.name);
    $('#empsal').val(record.name);
    $('#hra').val(record.name);
    $('#da').val(record.name);
    $('#deduct').val(record.name);

}
function resetForm(){
    $('#empid').val('');
     $('#empname').val('');
      $('#empsal').val('');
       $('#hra').val('');
        $('#da').val('');
         $('#deduct').val('');
          $('#empid').prop('disabled',false);
           $('#save').prop('diabled',true);
            $('#change').prop('disabled',true);
             $('#reset').prop('disabled',true);
             $('empId').focus();
}
function validateData(){
    var empid,empname,empsal,hra,da,deduct;
    empid=$("#empid").val();
    empname=$("#empname").val();
    empsal=$('#empsal').val();
    hra=$('#hra').val();
    da=$("#da").val();
    deduct=$("#deduct").val();
    if(empid === ''){
        alert("employee Id missing");
        $("#empid").focus();
        return "";
    }
    if(empname === ''){
        alert("employee name missing");
        $("#empname").focus();
        return "";
    }
    if(empsal === ''){
        alert("employee Id missing");
        $("#empid").focus();
        return "";
    }
    if(hra === ''){
        alert("hra missing");
        $("#hra").focus();
        return "";
    }
    if(da === ''){
        alert("da missing");
        $("#da").focus();
        return "";
    }
    if(deduct===''){
        alert("deduction missing");
        $("#deduct").focus();
        return "";
    }
    var jsonStrObj={
        id:empid,
        name:empname,
        salary:empsal,
        hra:hra,
        da:da,
        deduction:deduct
    };
    return JSON.stringify(jsonStrObj);
}
function getEmp(){
    var empIdJsonObj=getEmpIdAsJsonObj();
    var getRequest=createGET_BY_KEYRequest(connToken,empDBName,empRelationName,empIdJsonObj);
    jQuery.ajaxSetup({async: false});
    var resJsonObj=executeCommandAtGivenBaseUrl(getRequest,jpdbBaseURL,jpdbIRL);
    jQuery.ajaxSetup({async: true});
    if(resJsonObj.status===400){
        $("#save").prop('disabled',false);
        $("#reset").prop('disabled',false);
        $("#empname").focus();
    }
    else if(resJsonObj.status===200){
        $("#empid").prop('disabled',true);
        fillData(resJsonObj);
        $("#change").prop('disabled',false);
        $("#reset").prop('disabled',false);
        $("#empname").focus();
        function saveData(){
            var jsonStrObj=validateData();
            if(jsonStrObj === ''){
                return "";
            }
            var putRequest=createPUTrequest(connToken,jsonStrObj,empDBName,empRelationName);
            jQuery.ajaxSetup({async:false});
            var resJsonObj=executeCommandAtGivenBaseUrl(putRequest,jpdbBaseURL,jpdbIML);
             jQuery.ajaxSetup({async:true});
             resetForm();
             $('#empid').focus();
        }
        function changeData(){
            $('#change').prop('disabled',true);
            jsonChg=validateData();
            var updateRequest=createUPDATERecordRequest(connToken,jsonChg,empDBName,empRelationName,localStorage.getItem('recno'));
            jQuery.ajaxSetup({async:false});
            var resJsonObj=executeCommandAtGivenBaseUrl(updateRequest,jpdbBaseURL,jpdbIML);
            jQuery.ajaxSetup({async:true});
            resetForm();
            $('#empid').focus();
            
            
        }
    }
}

<!DOCTYPE html>
<!--
To change this license header, choose License Headers in Project Properties.
To change this template file, choose Tools | Templates
and open the template in the editor.
-->
<html>
    <head>
        <title>TODO supply a title</title>
        <meta charset="windows-1252">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
    </head>
    <body>
        <div class="container">
            <div class="page-header text-center"></div>
            <h2>Employee form using jpdb api</h2>
        </div>
        <form id="empform" method="get">
            <div class="form-group"><!-- comment -->
                <label>Employee Id</label>
                <input type="text" id='empid' class="form-control" onchange="getEmp()"><!-- comment -->
            </div>
             <div class="form-group"><!-- comment -->
                <label>Employee Name</label>
                <input type="text" id='empname' class="form-control"><!-- comment -->
            </div>
             <div class="form-group"><!-- comment -->
                <label>Basic salary</label>
                <input type="number" id='empsal' class="form-control"><!-- comment -->
            </div>
             <div class="form-group"><!-- comment -->
                <label>HRA</label>
                <input type="number" id='hra' class="form-control"><!-- comment -->
            </div>
             <div class="form-group"><!-- comment -->
                <label>DA</label>
                <input type="text" id='da' class="form-control"><!-- comment -->
            </div>
             <div class="form-group"><!-- comment -->
                <label>Deduction</label>
                <input type="number" id='deduct' class="form-control">
                <!-- comment -->
            </div>
             <div class="form-group text-center"><!-- comment -->
                <label>Employee Id</label>
                <button type="button" class='btn btn-lg btn-primary' id='save' onclick='saveData()' disabled>Save</button>
                        <button type="button" class='btn btn-lg btn-primary' id='change' onclick='changeData()' disabled>Change</button>
                         <button type="button" class='btn btn-lg btn-primary' id='reset' onclick='resetForm()' disabled>Reset</button>  
            </div>
        </form>
        <script type="text/javascript" src='http://login2explore.com/jpdb/resources/js/0.0.3/jpdb-commons.js'></script>
        <script type="text/javascript" src='js/newjavascript.js'></script>
    </body>
</html>
