
For Getting All Admin Users and using Datatables to update tables in View

[HttpPost]
        public JsonResult Get_Admin_Users()
        {
            IEnumerable<tbl_admin_user> users = DataAccessLayer.ExecuteQuery<tbl_admin_user>("sp_bind_admin_users", null);
            return Json(new { msg = users }, JsonRequestBehavior.AllowGet);
        }

Stored Procedure -- [sp_bind_admin_users]:

In View:

<link href="/js/datatables/datatables.min.css" rel="stylesheet" type="text/css" />
<link href="/js/datatables/plugins/bootstrap/datatables.bootstrap.css" rel="stylesheet" type="text/css" />
<link href="/js/bootstrap-toggle/bootstrap-toggle.min.css" rel="stylesheet" />

<div class="row">
    <div class="col-md-12">

        <section class="panel">
            <header class="panel-heading">
                Manage Admin / Employee Users
            </header>
            <div class="panel-body">
                <div class="col-md-12" style="padding-bottom:10px;">
                    <button type="button" class="btn btn-danger pull-right" data-backdrop="static" data-toggle="modal" href="#modaladduser">Add New User</button>
                </div>
                <div class="col-md-12">
                    <table id="admin_user_table" cellpadding="0" cellspacing="0" border="0" class="display table table-bordered" >
                        <thead>
                            <tr>
                                <th>#</th>
                                <th>Role</th>
                                <th>First Name</th>
                                <th>Last Name</th>
                                <th>Email</th>
                                <th>Department</th>
                                <th>Employee Code</th>
                                <th>Created Date</th>
                                <th>Active</th>
                                <th class="noExport"></th>
                                <th class="noExport"></th>
                            </tr>
                        </thead>
                        @*<tbody></tbody>*@
                    </table>
                </div>
            </div>
        </section>
    </div>
</div>

<script src="/js/datatables/datatables.min.js" type="text/javascript"></script>
<script src="/js/datatables/plugins/bootstrap/datatables.bootstrap.js" type="text/javascript"></script>
<script src="/js/bootstrap-toggle/bootstrap-toggle.min.js"></script>
<script src="~/custom_js/UpdateTable.js"></script>
Updatetable.js:
$(document).ready(function () {
var table = $('#admin_user_table');
 loadtable();
  function loadtable() {
       
        var oTable = table.dataTable({
         "fnDrawCallback": function () {
              
            $('.one').bootstrapToggle();            //important for when bootstrap toggle not working 
            

              },
            language: {
                searchPlaceholder: "Search.....",
                aria: {
                    sortAscending: ": activate to sort column ascending",
                    sortDescending: ": activate to sort column descending"
                },
                emptyTable: "No Admin User Found.",
                info: "Showing _START_ to _END_ of _TOTAL_ entries",
                infoEmpty: "No Admin User Found.",
                infoFiltered: "(filtered1 from _MAX_ total entries)",
                lengthMenu: "_MENU_ entries",
                search: "",
                zeroRecords: "No Admin User Found."
            },
          
            ajax: {
                
                url: "/Controller/Get_Admin_Users",
                type: "POST",
                dataSrc: function (res) {
                  
                    return res.msg;
                }
            },
            buttons: [{
                extend: 'collection',
                text: 'Export ...',
                buttons: [{
                    extend: 'pdf',
                    title: 'Error Logs',
                    exportOptions: {
                        columns: "thead th:not(.noExport)"
                    }
                }, {
                    extend: 'csv',
                    title: 'Error Logs',
                    exportOptions: {
                        columns: "thead th:not(.noExport)"
                    }
                }, {
                    extend: 'print',
                    title: 'Error Logs',
                    exportOptions: {
                        columns: "thead th:not(.noExport)"
                    }
                }
                ]
            }
            ],

            responsive: {
                details: {

                }
            },
            bAutoWidth: false,
            columnDefs: [
            {
                targets: 0,
                data: "AdminID"
            },

           {
               targets: 1,
               data: "RoleName",
           },

           {
               targets: 2,
               data: "FirstName"
           },

            {
                targets: 3,
                data: "LastName"
            },

            {
                targets: 4,
                data: "Email"
            },
            {
                targets: 5,
                //data: "DeptName"
                data: null,
                mRender: function (data, type, full)
                { return data.DeptName == 'Null' ? data.DeptName = '' : data.DeptName; }
            },
             {
                 targets: 6,
                 data: "EmployeeCode"
                 //data: null,
                 //mRender: function (data, type, full)
                 //{ return data.DeptName == 'Null' ? data.DeptName = '' : data.DeptName; }
             },


            {
                targets: 7,
                data: null,
                mRender: function (data, type, full) {
                    return moment(data.CreatedDate).format("YYYY-MM-DD HH:mm:ss");
                }
            },
             {
                targets: 8,
                data: null,
                mRender: function (data, type, full)
                { return data.Active == false ? '<span class="label label-danger"> Inactive </span>' : '<span class="label label-primary"> Active </span>'; }
            },
             {
                 targets: 9,
                 data: null,
                 mRender: function (data, type, full)
                 { return '<button type="button" class="btn btn-round btn-warning" href="javascript:void(0);" onclick="OpenEditForm(' + data.AdminID + ');">Edit</button>'; }
             },

             {
                 targets: 10,
                 data: null,
                 mRender: function (data, type, full)
                 { return '<button type="button" class="btn btn-round btn-danger" href="javascript:void(0);" onclick="DeleteUser(' + data.AdminID + ');">Delete</button>'; }
             },


         
           
            
            ],

            order: [0, 'asc'],

            lengthMenu: [
                [5, 10, 15, 20, -1],
                [5, 10, 15, 20, "All"]
            ],

            pageLength: 10,
            pagingType: "bootstrap_full_number",


            dom: "<'row' <'col-md-12 col-sm-12 col-xs-12 pull-right'B>><'row'<'col-md-6 col-sm-12 col-xs-12 pull-left'l><'col-md-6 col-sm-12 col-xs-12'f>r><'table-scrollable't><'row'<'col-md-5 col-sm-12'i><'col-md-7 col-sm-12'p>>"
        });
    }
    });

