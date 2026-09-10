{% extends 'backend/base.html' %}
{% load static %}

{% block title %}Job Applications{% endblock %}

{% block body %}
<div class="content-page">
    <div class="content">
        <div class="container-fluid">
            
            <div class="row">
                <div class="col-12">
                    <div class="page-title-box">
                        <h4 class="page-title">Job Applications</h4>
                    </div>
                </div>
            </div>

            <div class="row">
                <div class="col-12">
                    <div class="card">
                        <div class="card-body">

                            {% if messages %}
                                {% for msg in messages %}
                                    <div class="alert alert-{{ msg.tags }} alert-dismissible fade show" role="alert">
                                        {{ msg }}
                                        <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close"></button>
                                    </div>
                                {% endfor %}
                            {% endif %}

                            <div class="row mb-2">
                                <div class="col-sm-5">
                                    <a href="{% url 'backend:add_job_application' %}" class="btn btn-info mb-2">
                                        <i class="mdi mdi-plus-circle me-2"></i>Add Job Application
                                    </a>
                                </div>
                            </div>

                            <div class="table-responsive">
                                <table class="table table-striped">
                                    <thead>
                                        <tr>
                                            <th>ID</th>
                                            <th>Name</th>
                                            <th>Country</th>
                                            <th>Job Position</th>
                                            <th>Email</th>
                                            <th>Phone</th>
                                            <th>Japanese Level</th>
                                            <th>Experience</th>
                                            <th>Resume</th>
                                            <th>Date</th>
                                            <th>Action</th>
                                        </tr>
                                    </thead>
                                    <tbody>
                                        {% for application in applications %}
                                            <tr>
                                                <td>{{ forloop.counter }}</td>
                                                <td>{{ application.full_name }}</td>
                                                <td>
                                                    {% if application.country %}
                                                        {{ application.country.name }}
                                                    {% else %}
                                                        <span class="text-muted">General / Unspecified</span>
                                                    {% endif %}
                                                </td>
                                                <td>{{ application.job_title }}</td>
                                                <td>{{ application.email }}</td>
                                                <td>{{ application.phone }}</td>
                                                <td>{{ application.get_japanese_level_display }}</td>
                                                <td>{{ application.get_experience_display }}</td>
                                                <td>
                                                    {% if application.resume %}
                                                        <a href="{{ application.resume.url }}" target="_blank" class="text-info">View CV</a>
                                                    {% else %}
                                                        No CV
                                                    {% endif %}
                                                </td>
                                                <td>{{ application.created_at|date:"Y-m-d" }}</td>
                                                <td>
                                                    <a href="{% url 'backend:edit_job_application' application.id %}" class="text-info me-2">
                                                        <i class="mdi mdi-pencil"></i>
                                                    </a>
                                                    <a href="#" class="delete" data-target="#deleteJobApplicationModal{{ application.id }}">
                                                        <i class="mdi mdi-delete text-danger"></i>
                                                    </a>
                                                </td>
                                            </tr>

                                            <div class="modal fade" id="deleteJobApplicationModal{{ application.id }}" tabindex="-1" aria-hidden="true">
                                                <div class="modal-dialog">
                                                    <div class="modal-content custom-modal">
                                                        <div class="modal-header">
                                                            <h5 class="modal-title">Delete Job Application</h5>
                                                            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                                                        </div>
                                                        <div class="modal-body">
                                                            Are you sure you want to delete the application from "{{ application.full_name }}" for "{{ application.job_title }}"?
                                                        </div>
                                                        <div class="modal-footer">
                                                            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancel</button>
                                                            <form method="post" action="{% url 'backend:delete_job_application' application.id %}">
                                                                {% csrf_token %}
                                                                <button type="submit" class="btn btn-danger">Delete</button>
                                                            </form>
                                                        </div>
                                                    </div>
                                                </div>
                                            </div>
                                        {% empty %}
                                            <tr>
                                                <td colspan="11" class="text-center">No job applications found.</td>
                                            </tr>
                                        {% endfor %}
                                    </tbody>
                                </table>
                            </div>

                            <nav>
                                <ul class="pagination pagination-rounded mb-0">
                                    {% if applications.has_previous %}
                                        <li class="page-item"><a href="?page=1" class="page-link">First</a></li>
                                        <li class="page-item"><a href="?page={{ applications.previous_page_number }}" class="page-link">Previous</a></li>
                                    {% endif %}

                                    {% for num in applications.paginator.page_range %}
                                        <li class="page-item {% if applications.number == num %}active{% endif %}">
                                            <a href="?page={{ num }}" class="page-link">{{ num }}</a>
                                        </li>
                                    {% endfor %}

                                    {% if applications.has_next %}
                                        <li class="page-item"><a href="?page={{ applications.next_page_number }}" class="page-link">Next</a></li>
                                        <li class="page-item"><a href="?page={{ applications.paginator.num_pages }}" class="page-link">Last</a></li>
                                    {% endif %}
                                </ul>
                            </nav>

                        </div>
                    </div>
                </div>
            </div>

        </div>
    </div>
</div>

<style>
.custom-modal { background-color: white; }
</style>

<script>
$(document).ready(function () {
    $('.delete').click(function (e) {
        e.preventDefault();
        var modalId = $(this).data('target');
        $(modalId).modal('show');
    });
});
</script>
{% endblock %}
