== A simple single page AJAX CRUD todo list concept for laravel ==
   ------------------------------------------------------------

The folllowing is a simple CRUD concept on a single page with the use of modals and AJAX in laravel. 
Its almost a plug n play for laravel simple project or as a drop in script for that impromptu page.
It could be adapted to anywhere markup and javascript works.


HTML Markup:
------------
It defines a table to display todos with columns for Title, View/edit and Delete.
It includes a button to add new todos which triggers a modal (createTodoModal).
It includes modals for creating and updating todos (createTodoModal and updateTodoModal respectively).
Inside these modals, there are forms for inputting todo details like title. The forms are submittted asyncronously via
AJAX.

JavaScript:
-----------
It contains event listeners and AJAX requests to handle CRUD operations for todos.
There's a function getTodos() to fetch all todos via AJAX and populate the table. Makes GET request to /projects/todos/
The $("#createTodo").click function handles the submission of the form to create a new todo. Makes POST request to
/projects/todos/
Functions like setUpRemoveTodoListener(), setupTodoFormModalEventListeners(), and setupTodoFormEventListeners() handle
various aspects of interaction with the modals and forms.
There are AJAX calls to create, update, and delete todos.


USAGE:
------
Simply Copy and paste into your Laravel Project or any PHP based application,
Edit forms and ajax urls to match your controller routes
Remember to remove the {{}} Laravel blade sysntax to match your environment
For production setups, Just use Laravel Livewire instead and enjoy an SPA Front End Framework Experience


The code
---------

```

<!--start todos table simple -->

<div class="row">
    <div class="col-xl-12 col-md-12 col-sm-12 col-12">
        <h4 class="mb-4">Todo
            <button type="button" data-toggle="modal" data-target="#createTodoModal"
                class="btn btn-rounded btn-sm btn-danger" style="position:absolute;right:50px;">
                <span>Add Todo +</span>
            </button>
        </h4>
    </div>
</div>


<div id="" class="row">
    <div class="table-responsive">
        <table class="table table-bordered mb-4">
            <thead>
                <tr>
                    <th>Title</th>
                    <th></th>
                    <th></th>
                </tr>
            </thead>
            <tbody id="todo-table">
            </tbody>
        </table>
    </div>
</div>

<!--end todos list table -->

<!-- create todo modal -->
<div class="modal fade" id="createTodoModal" tabindex="-1" role="dialog" aria-labelledby="stkPushRequestModal"
    aria-hidden="true">
    <div class="modal-dialog modal-lg" role="document">
        <div class="modal-content">
            <div class="modal-header p-4 m-2" style="position:relative;">
                <h5 class="modal-title" id="exampleModalLabel">Create New Todo
                    <span> </span>
                </h5>
            </div>
            <div class="modal-body">
                <div class="">
                    <form method="POST" class="" id="create-todo-form" enctype="multipart/form-data">
                        {{ csrf_field() }}
                        <input type="hidden" name="project_id" value="{{ $project->id }}">
                        <div class="row">
                            <div class="col">
                                <div class="form-group mb-4">
                                    <label for="title">Title <span class="text-warning"></span></label>
                                    <input type="text" name="title" class="form-control" id="title">
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="col">
                                <div class="text-right">
                                    <button class="btn btn-outline-warning btn-rounded pull-left"
                                        data-dismiss="modal">Not now</button>&nbsp;
                                    <button id="createTodo" data-id="{{ $project->id }}"
                                        class="btn btn-danger btn-rounded">Create</button><br />
                                </div>
                            </div>
                        </div>
                    </form>
                </div>
            </div>
            <div>
            </div>
        </div>
    </div>
    <p id="airtime_balance">loading... </p>
</div>
<!--end create todo modal -->


<!-- update todo modal -->
<div class="modal fade" id="updateTodoModal" tabindex="-1" role="dialog" aria-labelledby="stkPushRequestModal"
    aria-hidden="true">
    <div class="modal-dialog modal-lg" role="document">
        <div class="modal-content">
            <div class="modal-header p-4 m-2" style="position:relative;">
                <h5 class="modal-title" id="exampleModalLabel">Update Todo&nbsp;&nbsp;&nbsp;
                    <span> </span>
                </h5>
            </div>
            <div class="modal-body">
                <div class="">
                    <form method="POST" class="" id="update-todo-form" enctype="multipart/form-data">
                        {{ csrf_field() }}
                        <input type="hidden" name="project_id" id="todoProjectId">
                        <input type="hidden" name="id" id="todoId">
                        <input type="hidden" name="_method" value="PUT">
                        <div class="row">
                            <div class="col">
                                <div class="form-group mb-4">
                                    <label for="title">Title <span class="text-warning"></span></label>
                                    <input type="text" name="title" class="form-control" id="todoTitle">
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="col">
                                <div class="text-right">
                                    <button class="btn btn-outline-warning btn-rounded pull-left"
                                        data-dismiss="modal">Not now</button>&nbsp;
                                    <button id="updateTodo" data-id="{{ $project->id }}"
                                        class="btn btn-danger btn-rounded">Create</button><br />
                                </div>
                            </div>
                        </div>
                    </form>
                </div>
            </div>
            <div>
            </div>
        </div>
    </div>
</div>
<!--end todo modal -->


<script>
    $("#createTodo").click(function (event) {
        event.preventDefault();
        var projectId = $(this).data("id");
        $.ajax({
            type: 'POST',
            url: "/projects/" + projectId + "/todos",
            headers: {
                'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
            },
            contentType: false,
            cache: false,
            processData: false,
            data: new FormData(document.getElementById('create-todo-form')),
            beforeSend: function (xhr, settings) { },
            success: function () {
                console.log('creating todo');
            },
            error: function (xhr) {
                console.log(xhr); // console.log(xhr.status + " " + xhr.statusText + " " + xhr.responseText);
            },
            timeout: 3000 // sets timeout to 3 seconds
        })
            .done(function (response) {
                console.log(response.data)
                //$("#").text(response.);
                Snackbar.show({
                    text: "Todo Update:" + response.status,
                    duration: 10000
                });
                $('#createTodoModal').modal('hide');
                $('.modal-backdrop').hide(); // for black background
                document.getElementById("create-todo-form").reset();
                getTodos();
                return;
            });
    });

    function setUpRemoveTodoListener() { //call in done function of getTodos to allow DOM render
        $(".removetodo").click(function (event) { //dont use ID in place of class since they wont be unique
            event.preventDefault();
            console.log('start deleting')
            var projectId = $(this).data("project_id");
            var todoId = $(this).data("id");
            $.ajax({
                type: 'DELETE',
                url: "/projects/" + projectId + "/todos/" + todoId,
                headers: {
                    'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
                },
                contentType: false,
                cache: false,
                processData: false,
                beforeSend: function (xhr, settings) { },
                success: function () {
                    console.log('deleting todo');
                },
                error: function (xhr) {
                    console.log(xhr); // console.log(xhr.status + " " + xhr.statusText + " " + xhr.responseText);
                },
                timeout: 3000 // sets timeout to 3 seconds
            })
                .done(function (response) {
                    console.log(response.data)
                    //$("#").text(response.);
                    alert("Deleted");
                    getTodos();
                    return;
                });
        });
    }
    function setupTodoFormModalEventListeners() {
        $(".updateTodoModalTrigger").click(function (event) {
            $('#updateTodoModal').modal('show');
            $(".modal-body #todoId").val($(this).data('id'));
            $(".modal-body #todoTitle").val($(this).data('title'));

        });
    }
    //handle edit form submission via ajax here and update dom element with said id well,
    //its easier to remove it from dom and append it.
    function setupTodoFormEventListeners() {
        $("#updateTodo").click(function (event) {
            event.preventDefault();
            var formId = 'update-todo-form';
            var form = $('.modal-body #' + formId)[0];
            var formData = new FormData(form);
            $.ajax({
                type: 'POST',
                url: "/projects/{{ $project->id }}/todos",
                headers: {
                    'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
                },
                contentType: false,
                cache: false,
                processData: false,
                data: formData, //new FormData(document.getElementById('update-todo-form')),
                beforeSend: function (xhr, settings) { },
                success: function () {
                    console.log('updating todo');
                },
                error: function (xhr) {
                    console.log(xhr); //
                    console.log(xhr.status + " " + xhr.statusText + " " +
                        xhr.responseText);
                },
                timeout: 3000 // sets timeout to 3 seconds
            })
                .done(function (response) {
                    console.log(response.data)
                    //$("#").text(response.);
                    var todo = response.data;
                    var element = document.getElementById('todo' + response.data
                        .id);
                    element.remove();
                    alert("Updated");
                    getTodos();
                    $('#updateTodoModal').modal('hide');
                    $('.modal-backdrop').hide(); // for black background
                    //document.getElementById("update-todo-form").reset();
                });
        });
    }

    //call when document ready
    $(function () {
        getTodos();
    });

    //getTodos
    function getTodos() {
        $.ajax({
            type: 'GET',
            url: "/projects/{{ $project->id }}/todos",
            headers: {
                'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
            },
            contentType: false,
            cache: false,
            processData: false,
            //data: new FormData(document.getElementById('create-todo-form')),
            beforeSend: function (xhr, settings) { },
            success: function () {
                console.log('fetching todos');
            },
            error: function (xhr) {
                console.log(
                    xhr); // console.log(xhr.status + " " + xhr.statusText + " " + xhr.responseText);
            },
            timeout: 3000 // sets timeout to 3 seconds
        })
            .done(function (response) {
                console.log(response.data)
                //$("#").text(response.);
                Snackbar.show({
                    text: "Todos fetch:" + response.status,
                    duration: 10000
                });
                //REMOVE EXISTING table rows
                var parent = document.getElementById("todo-table");
                while (parent.firstChild) {
                    parent.removeChild(parent.firstChild);
                }
                var newTodos = "";
                response.data.forEach(function (todo) {
                    newTodos += `<tr id="${'todo' + todo.id}">
                                        <td>${todo.title}</td>
                                        <td class="text-center"><span class="text-success">${todo.status}</span></td>
                                        <td><span data-id="${todo.id}"
                                        data-project_id="${todo.project_id}"
                                        data-title="${todo.title}"
                                        class="btn btn-danger updateTodoModalTrigger">View</span></td>
                                        <td class="text-center"><span data-id="${todo.id}"
                                        data-project_id="${todo.project_id}" class="removetodo">Delete</span></td>
                                    </tr>`;
                });
                //create fresh todos on dom
                document.getElementById("todo-table").insertAdjacentHTML('beforeend', newTodos);
                //now allow event listeners to edit the todo since the todo is now on DOM
                setUpRemoveTodoListener(); //delete todo listener
                setupTodoFormModalEventListeners(); //allow edit modal popup
                setupTodoFormEventListeners(); //update todo form submit listener
            });
    }

</script>
```



Improvements
------------

== To make it more reusable ==

Separate JavaScript and HTML: Move the JavaScript code to a separate file and include it where needed. This promotes
modularity and reusability.
Parameterize IDs: Instead of hardcoding IDs like createTodoModal, make them parameterized so the same modal can be
reused for different entities.
Parameterize URLs: Instead of hardcoding URLs like "/projects/" + projectId + "/todos", make them parameterized to
handle different routes.
Parameterize Data Handling: Instead of hardcoded form data handling in AJAX requests, make it dynamic to handle various
forms.
Encapsulate AJAX Calls: Encapsulate AJAX calls in reusable functions to handle CRUD operations for different entities.
Use Templates: Utilize templating engines to generate HTML dynamically based on data. This allows for easy reuse and
modification of UI elements.
