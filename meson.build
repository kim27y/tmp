project('triton-core',
        'cpp',
        version: '24.05',
        default_options : ['cpp_std=c++17']
)

# 기존 cmake에선 configure_file(libtritonserver.ldscript libtritonserver.ldscript COPYONLY)
configure_file(input: 'src/libtritonserver.ldscript', output: 'libtritonserver.ldscript', copy: true)

source_files = files(
    'src/backend_config.cc',
    'src/backend_manager.cc',
    'src/backend_memory_manager.cc',
    'src/backend_model.cc',
    'src/backend_model_instance.cc',
    'src/buffer_attributes.cc',
    'src/cache_entry.cc',
    'src/cache_manager.cc',
    'src/cuda_utils.cc',
    'src/dynamic_batch_scheduler.cc',
    'src/ensemble_scheduler/ensemble_scheduler.cc',
    'src/ensemble_scheduler/ensemble_utils.cc',
    'src/ensemble_scheduler/ensemble_model.cc',
    'src/filesystem/api.cc',
    'src/infer_parameter.cc',
    'src/infer_request.cc',
    'src/infer_response.cc',
    'src/infer_stats.cc',
    'src/infer_trace.cc',
    'src/instance_queue.cc',
    'src/label_provider.cc',
    'src/memory.cc',
    'src/metric_model_reporter.cc',
    'src/metrics.cc',
    'src/metric_family.cc',
    'src/model.cc',
    'src/model_config_utils.cc',
    'src/model_repository_manager/model_lifecycle.cc',
    'src/model_repository_manager/model_repository_manager.cc',
    'src/numa_utils.cc',
    'src/payload.cc',
    'src/pinned_memory_manager.cc',
    'src/rate_limiter.cc',
    'src/repo_agent.cc',
    'src/scheduler_utils.cc',
    'src/sequence_batch_scheduler/sequence_batch_scheduler.cc',
    'src/sequence_batch_scheduler/sequence_utils.cc',
    'src/sequence_state.cc',
    'src/server.cc',
    'src/shared_library.cc',
    'src/status.cc',
    'src/tritoncache.cc',
    'src/tritonserver.cc',
)

header_files = files(
    'src/backend_config.h',
    'src/backend_manager.h',
    'src/backend_memory_manager.h',
    'src/backend_model.h',
    'src/backend_model_instance.h',
    'src/buffer_attributes.h',
    'src/cache_entry.h',
    'src/cache_manager.h',
    'src/constants.h',
    'src/cuda_utils.h',
    'src/dynamic_batch_scheduler.h',
    'src/ensemble_scheduler/ensemble_scheduler.h',
    'src/ensemble_scheduler/ensemble_utils.h',
    'src/ensemble_scheduler/ensemble_model.h',
    'src/filesystem/api.h',
    'src/infer_parameter.h',
    'src/infer_request.h',
    'src/infer_response.h',
    'src/infer_stats.h',
    'src/infer_trace.h',
    'src/instance_queue.h',
    'src/label_provider.h',
    'src/memory.h',
    'src/metric_model_reporter.h',
    'src/metrics.h',
    'src/metric_family.h',
    'src/model_config_utils.h',
    'src/model.h',
    'src/model_repository_manager/model_lifecycle.h',
    'src/model_repository_manager/model_repository_manager.h',
    'src/numa_utils.h',
    'src/payload.h',
    'src/pinned_memory_manager.h',
    'src/rate_limiter.h',
    'src/repo_agent.h',
    'src/response_allocator.h',
    'src/scheduler.h',
    'src/scheduler_utils.h',
    'src/sequence_batch_scheduler/sequence_batch_scheduler.h',
    'src/sequence_batch_scheduler/sequence_utils.h',
    'src/sequence_state.h',
    'src/server.h',
    'src/server_message.h',
    'src/shared_library.h',
    'src/status.h',
    'src/tritonserver_apis.h',
)

dependencies = []
# conan
boost_dep    = dependency('boost', version: '>=1.78', required: true)
protobuf_dep = dependency('protobuf', required: true)
gtest_dep    = dependency('gtest', required: true)
c_ares_dep   = dependency('libcares', required: true)
re2_dep      = dependency('re2', required: true)
# system
thread_dep = dependency('threads', required: true)
dl_dep     = dependency('dl', required: true)
numa_dep   = dependency('numa', required: true)

triton_common_subproj = subproject('triton-common')
triton_common_async_work_queue_dep = triton_common_subproj.get_variable('triton_common_async_work_queue_dep')
triton_common_error_dep = triton_common_subproj.get_variable('triton_common_error_dep')
triton_common_logging_dep = triton_common_subproj.get_variable('triton_common_logging_dep')
triton_common_model_config_dep = triton_common_subproj.get_variable('triton_common_model_config_dep')
triton_common_table_printer_dep = triton_common_subproj.get_variable('triton_common_table_printer_dep')
triton_common_thread_pool_dep = triton_common_subproj.get_variable('triton_common_thread_pool_dep')
triton_common_json_dep = triton_common_subproj.get_variable('triton_common_json_dep')

dependencies = [boost_dep,
                protobuf_dep,
                gtest_dep,
                c_ares_dep,
                re2_dep,
                thread_dep,
                dl_dep,
                numa_dep,
                triton_common_async_work_queue_dep,
                triton_common_thread_pool_dep,
                triton_common_error_dep,
                triton_common_model_config_dep,
                triton_common_logging_dep,
                triton_common_json_dep,
                triton_common_table_printer_dep]

compile_definition = ['-DTRITON_VERSION="2.43.0dev"', '-DTRITON_ENABLE_ENSEMBLE=1']

cpp_args = ['-Wall', '-Wextra', '-Wno-unused-parameter', '-Wno-deprecated-declarations', '-Werror']
link_args = ['-Wl,--version-script', meson.current_build_dir() + '/libtritonserver.ldscript'] # link_flags
link_depends = [meson.current_build_dir() + '/libtritonserver.ldscript']

include_header_dir = include_directories('src', 'include')

if get_option('enable_gpu')
    source_files += files('src/cuda_memory_manager.cc', 'src/cuda_block_manager.cc', 'src/model_config_cuda.cc')
    header_files  += files('src/cuda_memory_manager.h', 'src/cuda_block_manager.h', 'src/model_config_cuda.h')

    cuda_dep = dependency('cuda', required: true, modules: ['cudart'])
    cnmem_subproj = subproject('cnmem')
    cnmem_dep = cnmem_subproj.get_variable('cnmem_dep')

    cuda_path = get_option('CUDA_INCLUDE_DIRS')
    compile_definition += ['-DTRITON_ENABLE_GPU=1', '-DTRITON_MIN_COMPUTE_CAPABILITY=6.0']
    include_header_dir = include_directories('src', 'include', cuda_path)

    cuda_version = get_option('CUDA_VERSION')

    # 10.1 ㅇㅣ상이면 추가 TRITON_ENABLE_CUDA_GRAPH
    if cuda_version.version_compare('>=10.1')
        message('enable CUDA GRAPH')
        compile_definition += ['-DTRITON_ENABLE_CUDA_GRAPH=1']
    endif
    dependencies += [cuda_dep, cnmem_dep]
endif

triton_core_lib = shared_library(
    meson.project_name(),
    source_files,
    override_options: ['cpp_std=c++17'],
    cpp_args: cpp_args + compile_definition,
    dependencies: dependencies,
    link_args: link_args,
    link_depends: link_depends,
    include_directories: include_header_dir,
    version: meson.project_version(),
    install: true,
    install_dir: meson.project_build_root() / 'third_party' / 'lib',
)

install_header_dir = meson.project_build_root() / 'third_party' / 'include'
install_subdir('include', install_dir: install_header_dir,strip_directory: true)
triton_core_dep = declare_dependency(
    sources: header_files,
    include_directories: include_header_dir,
    link_with: triton_core_lib,
)


serverstub_source_files = files('src/tritonserver_stub.cc')

triton_core_serverstub = shared_library(
    'tritonserver',
    serverstub_source_files,
    cpp_args: cpp_args,
)
